

### 知识 - 标准中间格式
在知识构造的过程中，所有原始格式的文档，都需要考虑转换到一种统一的知识格式，方便后续进行知识的注入和召回优化。

```json
# paragraph 例子
{
    "page_content":"{will include original document string and sliced document string}",
    "metadata":{
        "content_type":"paragraph",
        "heading_hierarchy":{
        },
        "figure_list":[
        ],
        "chunk_id":"",
        "file_path":"",
        "keywords":[
        ],
        "summary":"",
  			"synthesis_qa" : [(Q, A),...],
    }
}

# FAQ 例子
{
    "page_content":"{Question}=>{Answer}",
    "metadata":{
        "content_type":"QA",
        "heading_hierarchy":{
        },
        "figure_list":[
        ],
        "chunk_id":"",
        "file_path":"",
        "keywords":[
        ],
        "summary":"",
      	"synthesis_qa" : [("Q", "A"),...],
    }
}

# table 例子
{
    "page_content":"{mmd fromat table data}",
    "metadata": {
        "content_type":"table",
        "heading_hierarchy":{
        },
        "figure_list":[
        ],
        "chunk_id":"",
        "file_path":"",
        "keywords":[
        ],
        "summary":"",
				"synthesis_qa" : [("Q", "A"),...],
    }
}
```



### 从长文档提取FAQ

- 适应场景
  文档中，上下文依赖比较严重，无论如何切分，都会存在上下文的信息缺失。chunk本身信息不完整。

- 做法思路
  利用Bedrock-Claude的大窗口能力，100k约等于100页文档。 直接基于全文提取FAQ，会存在大量的内容覆盖不到。优化后分两步走, 第一步，根据全文提取标题和各个章节的标题。 第二步，在Prompt中，限定在各个'章节'内提取QA。

- Usage
    ```shell
    # 输入的input_file 为纯文本文件，可以是txt，也可以是markdown
    python Enhance_Doc_Bedrock.py --input_file input.txt --output_file output.json
    ```



### OpenSource doc spliter(表格信息提取，需要结合OCR自行实现)

- 运行依赖
    ```shell
    pip install boto3
    pip install BeautifulSoup4
    pip install langchain==0.0.162
    pip install pdfminer.six
    ```

- Knowledge Document(PDF) For Test 
  + https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/ec2-ug.pdf
  + https://docs.aws.amazon.com/zh_cn/redshift/latest/dg/redshift-dg.pdf
  + https://docs.aws.amazon.com/zh_cn/whitepapers/latest/aws-overview/aws-overview.pdf

- Usage
    ```shell
    mkdir kg_dir
    # 指定 endpoint, 可以激活对长文本的摘要能力 --llm_endpoint ${llm_endpoint}
    python doc_spliter.py --input_file ec2-ug.pdf --output_dir ./kg_dir
    ```



### Textract-based doc spliter(支持表格信息提取)

- 运行依赖
    ```shell
    pip install boto3
    pip install BeautifulSoup4
    pip install pdfminer.six
    conda install -c conda-forge poppler
    pip install pdf2image
    pip install tqdm
    ```
    如果存在依赖问题， 参考https://github.com/Belval/pdf2image 的README中的安装步骤

- Knowledge Document(PDF) For Test 
  + ./common-stock-fs.pdf

- Usage
  
    输出为输入的同名目录
    
    ```shell
    python pdf_spliter.py --input_file ../docs/common-stock-fs.pdf
    ```