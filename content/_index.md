# Cách tạo Agent với LlamaIndex

> **📖 Bài viết gốc**: [How to create agents with LlamaIndex](https://www.pluralsight.com/resources/blog/ai-and-data/creating-agents-with-llamaindex)  
> **👤 Tác giả**: Axel Sirota - Microsoft Certified Trainer  
> **📅 Ngày xuất bản**: 14/08/2025  
> **🌐 Nguồn**: [pluralsight](https://www.pluralsight.com/resources/blog)  
> **👨‍💻 Người dịch**: Ngô Thành Vinh - FCJ Intern  
> **📅 Ngày dịch**: 19/08/2025  
> **⏱️ Thời gian đọc**: 9 phút

---

## 📋 Tóm tắt

Bài viết hướng dẫn cách xây dựng một **Knowledge Retrieval Agent** - một Agent có khả năng truy xuất thông tin từ các tập dữ liệu bên ngoài và đưa ra câu trả lời phù hợp. Để làm điều này, tác giả giới thiệu về LlamaIndex – công cụ giúp kết nối ngôn ngữ lớn và dữ liệu ngoài bằng cách sử dụng chỉ mục (index) để truy vấn hiệu quả. Bài viết hướng dẫn từng bước từ việc cài đặt môi trường, chuẩn bị dữ liệu, xây dựng index, truy vấn thông tin, tới cải thiện kết quả bằng Hugging Face, và kết nối với AWS S3 hay Azure Blob Storage. Và cuối cùng là các trường hợp sử dụng thực tiễn và lưu ý tối ưu hóa khi triển khai.

**🎯 Đối tượng đọc**: Developer/AI Engineer trình độ Intermediate muốn triển khai Agent có tính ứng dụng cao  
**📊 Độ khó**: Intermediate  
**🏷️ Tags**: LlamaIndex, Agent, Knowledge Retrieval

---

## 📚 Mục lục

- [Phần 1: Giới thiệu](#phần-1-giới-thiệu)
- [Phần 2: Agent là gì, và tại sao lại sử dụng LlamaIndex](#phần-2-ai-tự-chủ-là-gì-và-tại-sao-lại-sử-dụng-llamaindex)
- [Phần 3: Xây dựng Agent truy xuất thông tin](#phần-3-xây-dựng-ai-tự-chủ-truy-xuất-thông-tin)
- [Phần 4: Kết hợp với dữ liệu từ AWS S3 hoặc Azure Blob Storage](#phần-4-kết-hợp-với-dữ-liệu-từ-aws-s3-hoặc-azure-blob-storage)
- [Phần 5: Nên chọn phương pháp nào](#phần-5-nên-chọn-phương-pháp-nào)
- [Phần 6: Ứng dụng thực tế](#phần-6-ứng-dụng-thực-tế)
- [Phần 7: Xử lý lỗi thường gặp và mẹo tối ưu](#phần-7-xử-lý-lỗi-thường-gặp-và-mẹo-tối-ưu)
- [Phần 8: Kết luận](#phần-8-kết-luận)
- [Glossary - Thuật ngữ](#glossary---thuật-ngữ)
- [Tài liệu tham khảo](#tài-liệu-tham-khảo)

---

### Phần 1: Giới thiệu
Ngày nay trí tuệ nhân tạo (AI) đang định hình lại cách chúng ta sử dụng công nghệ và một trong những ứng dụng tiêu biểu là tạo ra các Agent thông minh. Các Agent này hoạt động tự động với khả năng trả lời câu hỏi, tìm kiếm thông tin và thậm chí là đưa ra quyết định. Ở trong bài viết này chúng ta sẽ đi sâu vào cách để **tạo ra một Agent có khả năng truy xuất thông tin bằng LlamaIndex**

Nếu bạn là một người đam mê AI với trình độ Intermediate và đã sẵn sàng để tạo ra một Agent. Đến cuối bài viết này, bạn không chỉ hiểu được các khái niệm đằng sau llamaindex mà bạn còn có được một Agent có thể truy xuất thông tin mà bạn có thể triển khai được
### Phần 2: Agent là gì, và tại sao lại sử dụng LlamaIndex
Bạn hãy tưởng tượng ra một người thủ thư ảo, người mà có thể tìm kiếm thông tin trong hàng ngàn cuốn sách và đưa cho bạn thông tin về thứ bạn đang tìm. Đó chính xác là những gì Agent thực hiện. Nó sẽ tự động quản lý các nhiệm vụ, tìm kiếm thông tin và tương tác với người dùng để đưa ra kết quả

Nhưng vấn đề bây giờ là làm sao để cung cấp quyền hạn cho người thủ thư này quyền được truy cập vào các tài liệu văn bản, cơ sở dữ liệu và các bài viết? Đây chính là lúc mà chúng ta sử dụng LlamaIndex

#### LlamaIndex cầu nối giữa dữ liệu và ngôn ngữ lớn
LlamaIndex (hay trước đây còn gọi là GPT Index) đã đơn giản hóa quá trình tích hợp ngôn ngữ lớn như OpenAI GPT-4 với các dữ liệu bên ngoài một cách hiệu quả. Thay vì làm quá tải mô hình với lượng lớn dữ liệu đầu vào thì LlamaIndex cho phép AI truy vấn một chỉ mục đã được xây dựng sẵn dựa trên dữ liệu đã có, giúp các phản hồi nhanh và chính xác hơn
##### Các lợi ích bao gồm
 - Truy xuất thông tin từ loại dữ liệu có cấu trúc (ví dụ: table trong sql) và dữ liệu không có cấu trúc (ví dụ: văn bản)
 - Tương thích với OpenAI API và Hugging Face Transformers
 - Dễ triển khai để xây dựng Agent

### Phần 3: Xây dựng Agent truy xuất thông tin
Bây giờ chúng ta sẽ đi từng bước để xây dựng một Agent có thể truy xuất tri thức từ dữ liệu tùy chính của bạn
#### Bước 1: Cài đặt mỗi trường
Trước tiên cần phải tải các công cụ cần thiết bao gồm LlamaIndex, OpenAI’s API, and Hugging Face Transformers
```
pip install llama-index openai transformers
```
Bên cạnh đó bạn cũng cần OpenAI API Key. Nếu bạn chưa có đăng ký tại [OpenAI](https://openai.com/vi-VN/) và lưu trữ API Key của bạn. Ở trong code của bạn thêm vào đoạn code sau:
```
import os
# Nhập OpenAI API key của bạn ở đây
os.environ["OPENAI_API_KEY"] = "your-openai-api-key"
```
#### Bước 2: Chuẩn bị dữ liệu của bạn
Agent của bạn cần một dữ liệu gì đó để truy vấn vậy nên bạn cần chuẩn bị một bộ dữ liệu hoặc một bộ sưu tập các tài liệu văn bản hoặc bài viết mà Agent của bạn cần tìm kiếm

Cho ví dụ này, lưu trữ tệp văn bản vào thư mục có tên ```data/ ```. Sau đó dùng LlamaIndex gọi ```SimpleDirectoryReader``` để tải tài liệu vào chương trình
```
from llama_index import SimpleDirectoryReader

# Tải tài liệu từ thư mục data
documents = SimpleDirectoryReader('./data').load_data()
print(f"Loaded {len(documents)} documents!")
```
Nếu bạn không có tài liệu mẫu, thì nên tạo một vài file ```.txt``` với chủ đề như ứng dụng của Agent hoặc lợi ích của công nghệ. Điều này sẽ cung cấp cho Agent của bạn một cái gì đó để truy vấn
#### Bước 3: Tạo chỉ mục
Một chỉ mục giống như bản đồ tối ưu cho việc tìm kiếm dữ liệu của bạn. LlamaIndex cung cấp các loại chỉ mục khác nhau, chẳng hạn như chỉ mục dựa trên vector hoặc dựa trên từ khóa. Để đơn giản thì chúng ta sẽ tạo một chỉ mục dựa trên vector để tìm kiếm các đoạn văn bản tương tự

```
from llama_index import GPTVectorStoreIndex

# Xây dựng chỉ mục dựa vào tài liệu được tải lên
index = GPTVectorStoreIndex.from_documents(documents)

# Lưu lại chỉ mục để sử dụng sau
index.storage_context.persist('./index_storage')
```
Và đây là những gì diễn ra:
 - ```GPTVectorStoreIndex``` chuyển đổi dữ liệu của bạn thành các biểu diễn vector giúp việc tìm kiếm dễ dàng hơn
 - ```persist()``` sẽ lưu chỉ mục xuống ổ đĩa và bạn có thể sử dụng sau này
#### Bước 4: Truy vấn chỉ mục
Bây giờ chỉ mục đã sẵn sàng và truy vấn nó. Tiếp theo chúng ta sẽ tích hợp OpenAI API để xử lý truy vấn và trả lời
```
# Truy vấn chỉ mục
def query_index(query: str, index):
    response = index.query(query)
    return response.response

# Truy vấn mẫu
query = "What are the benefits of AI in healthcare?"
response = query_index(query, index)
print("Agent Response:", response)
```
Đây là một hàm đơn giản để nhận một truy vấn từ người dùng, đưa truy vấn qua chỉ mục và trả về phản hồi có liên quan nhất
#### Bước 5: Kết hợp Hugging Face để cải thiện câu trả lời
Trong khi các mô hình OpenAI thường rất tốt trong việc hiểu các truy vấn, bạn có thể sử dụng Hugging Face Transformers để tinh chỉnh các phản hồi. Chẳng hạn bạn có thể sử dụng mô hình tóm tắt để giảm bớt các câu trả lời dài dòng
```
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM

# Tải một mô hình Hugging Face
tokenizer = AutoTokenizer.from_pretrained("t5-large")
model = AutoModelForSeq2SeqLM.from_pretrained("t5-large")

# Tinh chỉnh phản hồi
def refine_response(query, raw_response):
    inputs = tokenizer(query + raw_response, return_tensors="pt", truncation=True)
    outputs = model.generate(**inputs, max_length=50)
    return tokenizer.decode(outputs[0], skip_special_tokens=True)

# Tinh chỉnh phản hồi của Agent
refined_response = refine_response(query, response)
print("Refined Response:", refined_response)
```
Ở trong ví dụ này:
 - Mô hình ```T5-large``` của Hugging Face tinh chỉnh phản hồi thô bằng cách tóm tắt hoặc diễn đạt lại
 - Bước này giúp cải thiện chất lượng câu trả lời, đặc biệt đối với dữ liệu dài dòng hoặc không có cấu trúc
### Phần 4: Kết hợp với dữ liệu từ AWS S3 hoặc Azure Blob Storage
Trong môi trường triển khai, các tệp dữ liệu thường nằm trên dịch vụ lưu trữ đám mây như AWS S3 hoặc Azure Blob Storage thay vì lưu trong file cục bộ
LlamaIndex cung cấp nhiều cách để làm việc với các hệ thống lưu trữ đám mây này, đảm bảo tính linh hoạt cho từng trường hợp sử dụng khác nhau. Bạn có thể chọn giữa
 - Tải thủ công: Tải file từ lưu trữ đám mây về và xử lý trên máy cục bộ.
 - Tích hợp trực tiếp với AWS S3: Sử dụng class ```SimpleS3Reader``` của LlamaIndex để truy cập liền mạch vào bucket S3.
 - Tích hợp trực tiếp với Azure Blob Storage: Sử dụng ```SimpleBlobReader``` để tải dữ liệu trực tiếp từ container của Azure Blob.
Bây giờ chúng ta sẽ tìm hiểu chi tiết từng lựa chọn
#### Trường hợp 1: Tải file về và xử lý thủ công
##### AWS S3: Tải thủ công
Chúng ta sẽ sử dụng thư viện ```boto3 ``` để lấy file từ bucket của S3 và lưu cục bộ, và tải dữ liệu bằng ```SimpleDirectoryReader```
```
import boto3
from llama_index import SimpleDirectoryReader
import os

# Cấu hình AWS S3
AWS_ACCESS_KEY = "your-aws-access-key"
AWS_SECRET_KEY = "your-aws-secret-key"
AWS_BUCKET_NAME = "your-s3-bucket-name"
LOCAL_DOWNLOAD_PATH = "./s3_data/"

# Khởi tạo S3
s3_client = boto3.client(
    's3',
    aws_access_key_id=AWS_ACCESS_KEY,
    aws_secret_access_key=AWS_SECRET_KEY
)

# Tải tất cả file từ S3 bucket
def download_s3_bucket(bucket_name, local_path):
    os.makedirs(local_path, exist_ok=True)
    response = s3_client.list_objects_v2(Bucket=bucket_name)
    for obj in response.get('Contents', []):
        s3_client.download_file(bucket_name, obj['Key'], os.path.join(local_path, obj['Key']))
        print(f"Downloaded: {obj['Key']}")

# Tải và nạp vào LlamaIndex
download_s3_bucket(AWS_BUCKET_NAME, LOCAL_DOWNLOAD_PATH)
documents = SimpleDirectoryReader(LOCAL_DOWNLOAD_PATH).load_data()
print(f"Loaded {len(documents)} documents from S3!")
```
##### Azure Blob Storage: Tải thủ công
Đối với Azure Blob Storage, chúng ta sử dụng thư viện ```azure-storage-blob``` theo cách tương tự như tìm và lưu vào file cục bộ
```
from azure.storage.blob import BlobServiceClient
from llama_index import SimpleDirectoryReader
import os

# Cấu hình Azure Blob Storage
AZURE_CONNECTION_STRING = "your-azure-connection-string"
AZURE_CONTAINER_NAME = "your-container-name"
LOCAL_DOWNLOAD_PATH = "./azure_blob_data/"

# Khởi tạo BlobServiceClient
blob_service_client = BlobServiceClient.from_connection_string(AZURE_CONNECTION_STRING)
container_client = blob_service_client.get_container_client(AZURE_CONTAINER_NAME)

# Tải toàn bộ blobs từ Azure container
def download_azure_container(container_client, local_path):
    os.makedirs(local_path, exist_ok=True)
    blob_list = container_client.list_blobs()
    for blob in blob_list:
        blob_client = container_client.get_blob_client(blob)
        with open(os.path.join(local_path, blob.name), "wb") as file:
            file.write(blob_client.download_blob().readall())
            print(f"Downloaded: {blob.name}")

# Tải files và nạp vào LlamaIndex
download_azure_container(container_client, LOCAL_DOWNLOAD_PATH)
documents = SimpleDirectoryReader(LOCAL_DOWNLOAD_PATH).load_data()
print(f"Loaded {len(documents)} documents from Azure Blob Storage!")
```
#### Trường hợp 2: Tích hợp trực tiếp AWS S3 bằng SimpleS3Reader
Nếu bạn muốn tiếp cận một cách tiếp cận gọn gàng hơn, LlamaIndex cung cấp class ```SimpleS3Reader``` để tích hợp trực tiếp với AWS S3. Class này sẽ tự động xử lý việc xác thực, truy xuất và phân tích dữ liệu ngầm ở sau
##### Code mẫu: sử dụng SimpleS3Reader
```
from llama_index import SimpleS3Reader

# Cấu hình AWS S3
AWS_ACCESS_KEY = "your-aws-access-key"
AWS_SECRET_KEY = "your-aws-secret-key"
AWS_BUCKET_NAME = "your-s3-bucket-name"

# Khởi tạo S3 reader
s3_reader = SimpleS3Reader(
    bucket=AWS_BUCKET_NAME,
    aws_access_key_id=AWS_ACCESS_KEY,
    aws_secret_access_key=AWS_SECRET_KEY,
)

# Tải tài liệu trực tiếp từ S3
documents = s3_reader.load_data()
print(f"Loaded {len(documents)} documents from S3!")
```
##### Cách hoạt động
 - Xác thực: ```SimpleS3Reader``` sử dụng AWS access key và secret key của bạn để xác thực với S3 bucket
 - Phân tích file: ```SimpleS3Reader``` lấy và xử lý tài liệu trực tiếp từ bucket mà không cần lưu vào bộ nhớ cục bộ
 - Tương thích với LlamaIndex: Tài liệu được nạp vào định dạng nội bộ của LlamaIndex, sẵn sàng cho việc lập chỉ mục và truy vấn
#### Trường hợp 3: Tích hợp trực tiếp Azure Blob Storage bằng SimpleBlobReader
Đối với Azure Blob Storage, LlamaIndex cung cấp class SimpleBlobReader giúp đơn giản hóa việc truy cập và tải dữ liệu từ Azure containers
##### Code mẫu: sử dụng SimpleBlobReader
```
from llama_index import SimpleBlobReader

# Cấu hình Azure Blob Storage
AZURE_CONNECTION_STRING = "your-azure-connection-string"
AZURE_CONTAINER_NAME = "your-container-name"

# Khởi tạo Blob reader
blob_reader = SimpleBlobReader(
    connection_string=AZURE_CONNECTION_STRING,
    container_name=AZURE_CONTAINER_NAME,
)

# Tải tài liệu trực tiếp từ Azure Blob Storage
documents = blob_reader.load_data()
print(f"Loaded {len(documents)} documents from Azure Blob Storage!")
```
##### Cách hoạt động
 - Xác thực: ```SimpleBlobReader``` sử dụng Azure connection string của bạn để xác thực với blob storage
 - Truy xuất blob: ```SimpleBlobReader``` lấy và xử lý tất cả các blob từ container đã chỉ định
 - Tương thích với LlamaIndex: Giống như S3 reader, ```SimpleBlobReader``` chuẩn bị tài liệu cho việc lập chỉ mục và truy vấn một cách liền mạch
### Phần 5: Nên chọn phương pháp nào
| Cách tiếp cận | Phương pháp |
|---------|------------|
| Tải thủ công | Nếu bạn cần toàn quyền kiểm soát các tệp đã tải xuống hoặc làm việc offline sau khi truy xuất dữ liệu |
| SimpleS3Reader | Nếu bạn sử dụng S3 và muốn tích hợp trực tiếp mà không cần quản lý việc tải xuống tài liệu thủ công |
| SimpleBlobReader | Nếu bạn sử dụng Azure Blob Storage và cần tích hợp liền mạch để lập chỉ mục và truy vấn thời gian thực |
#### Ưu điểm của các readers tích hợp sẵn
 - Hiệu quả: Tích hợp trực tiếp dịch vụ lưu trữ đám mây vào quy trình làm việc, giúp giảm bớt độ phức tạp
 - Xử lý theo thời gian thực: Lý tưởng cho các tập dữ liệu động hoặc thường xuyên được cập nhật
 - Hạn chế lặp code: Không cần dùng thêm các thư viện ngoài như boto3 hoặc azure-storage-blob, trừ khi bạn cần chức năng tùy chỉnh
### Phần 6: Ứng dụng thực tế
Dưới đây là một số ví dụ ứng dụng thực tế cho Agent truy xuất thông tin của bạn:
 - Chatbots hỗ trợ khách hàng
    + Huấn luyện Agent dựa trên sách hướng dẫn hoặc các câu hỏi thường gặp (FAQ) để cung cấp câu trả lời tức thì cho khách hàng.
 - Trợ lý nghiên cứu
    + Nạp thông tin các bài báo học thuật hoặc báo cáo, cho phép các nhà nghiên cứu dễ dàng truy xuất tóm tắt hoặc tài liệu tham khảo
 - Quản lý thông tin trong doanh nghiệp
    + Sử dụng Agent để tìm kiếm chính sách công ty, tài liệu nội bộ hoặc tài liệu đào tạo
Triển khai Agent có thể đơn giản như đóng gói một ứng dụng Flask hoặc triển khai lên nền tảng serverless như AWS Lambda.
### Phần 7: Xử lý lỗi thường gặp và mẹo tối ưu
Những lỗi thường gặp:
 - Lỗi bộ nhớ: Nếu bộ dữ liệu quá lớn, hãy dùng kỹ thuật chunking để chia tài liệu thành những phần nhỏ trước khi lập chỉ mục
 - Kết quả không nhất quán: Thử nghiệm với các tham số như temperature và max_tokens để kiểm soát độ sáng tạo và độ dài đầu ra của mô hình
 - Lỗi API: Đảm bảo API Keys chính xác và kiểm tra giới hạn tần suất đối với OpenAI và Hugging Face
Mẹo tối ưu hóa:
 - Truy vấn phân cấp: Dùng chỉ mục nâng cao cho các truy vấn nhiều tầng, ví dụ: tìm theo chủ đề trước, sau đó đi sâu vào chi tiết
 - Gắn thẻ metadata: Gắn thẻ tài liệu với metadata (ví dụ: tác giả, ngày tháng) để cải thiện độ liên quan của truy vấn
### Phần 8: Kết luận
Chúc mừng! Bạn vừa xây dựng một Agent truy xuất thông tin bằng LlamaIndex, OpenAI và Hugging Face. Bằng cách kết hợp các công cụ này, bạn đã tạo ra một hệ thống thông minh có khả năng truy vấn và truy xuất thông tin từ bất kỳ tập dữ liệu nào

Mọi khả năng ứng dụng là vô tận dù bạn xây dựng chatbot, trợ lý nghiên cứu hay công cụ doanh nghiệp, LlamaIndex cho phép bạn khai thác hiệu quả sức mạnh của các mô hình ngôn ngữ lớn.

Bạn đã sẵn sàng để tiến xa hơn chưa? Hãy thử nghiệm với các tập dữ liệu lớn hơn, mô hình tùy chỉnh trên Hugging Face hoặc các tính năng nâng cao của LlamaIndex. Tương lai của Agentic AI nằm trong tay bạn!

---

## 📖 Glossary - Thuật ngữ

| English | Tiếng Việt | Định nghĩa |
|---------|------------|------------|
| Agent | AI tự chủ | Phần mềm dựa trên trí tuệ nhân tạo, có khả năng tự động thực hiện nhiệm vụ như trả lời câu hỏi, tìm kiếm thông tin, phân tích dữ liệu hoặc đưa ra quyết định. Khác với chatbot thụ động chỉ phản hồi khi được hỏi, Agent có thể chủ động hành động dựa trên mục tiêu đã được giao |
| LLMs | Ngôn ngữ lớn | là các hệ thống trí tuệ nhân tạo (AI) tiên tiến được thiết kế để xử lý, hiểu và tạo văn bản giống con người |
| index | Chỉ mục | là một cấu trúc dữ liệu được sử dụng trong các hệ quản trị cơ sở dữ liệu và các công cụ tìm kiếm để tăng tốc độ truy vấn và tìm kiếm thông tin |
| dataset | Bộ dữ liệu | là tập hợp dữ liệu có tổ chức, đóng vai trò cốt lõi trong các lĩnh vực công nghệ như trí tuệ nhân tạo và học máy |
| production | Môi trường thực tế | môi trường triển khai hệ thống để phục vụ người dùng cuối |
| blob(Binary Large Object) | Đối tượng nhị phân lớn | là một kiểu dữ liệu trong cơ sở dữ liệu hoặc hệ thống lưu trữ, được dùng để chứa các dữ liệu nhị phân có kích thước lớn |
| Embedding | Biểu diễn vector | biến dữ liệu (ví dụ văn bản, tài liệu) thành một dãy số (vector) mà máy tính có thể hiểu và so sánh |
| Reader |  | Là một class trong LlamaIndex |
| Flask |  | là một framework web viết bằng Python, được dùng để xây dựng các ứng dụng web hoặc API |
## 🔗 Tài liệu tham khảo

### Tài liệu gốc
- [Original Article](https://www.pluralsight.com/resources/blog/ai-and-data/creating-agents-with-llamaindex): Bài viết gốc
- [Author's Profile](https://www.pluralsight.com/resources/blog/blog-author/axel-sirota): Thông tin tác giả

### Tài liệu tiếng Việt
- [LlamaIndex](https://www.studywithgpt.com/vi/tutorial/timkuq): Tài liệu Llamaindex tiếng Việt
- [S3](https://000057.awsstudygroup.com/vi/)
- [Azure Blob Storage](https://cloudvietnam18.wordpress.com/2019/09/15/huong-dan-tao-va-truy-cap-blob-storage-tren-microsoft-azure/)

### Tools và Services
- [LlamaIndex](https://www.llamaindex.ai/)  
- [OpenAI](https://openai.com/)  
- [Hugging Face Transformers](https://huggingface.co/)  
- [S3](http://console.aws.amazon.com/s3)
- [Azure Blob Storage](https://azure.microsoft.com/en-us/products/storage/blobs)
---

## 💬 Ghi chú của người dịch

### Challenges trong quá trình dịch
- **Technical Terms**: Cần giải thích rõ agent, embedding, LLMs để người đọc Việt Nam dễ hiểu.  
- **Cultural Context**: Điều chỉnh phong cách diễn đạt, giữ tính chuyên ngành nhưng dễ tiếp cận.  
- **Complex Concepts**: Các bước kỹ thuật cần viết mạch lạc, dễ hiểu.

### Insights gained
- **Technical Learning**: Biết cách tạo agent kết hợp LLM và dữ liệu đám mây.  
- **Language Skills**: Tăng khả năng dịch các khái niệm AI sang tiếng Việt.  
- **Industry Knowledge**: Thấy cách ứng dụng LLM trong hệ thống thực tế với AWS/Azure.

---

## 🤝 Đóng góp và Feedback

Bài dịch này được thực hiện trong khuôn khổ **FCJ Internship Program**. 

**📧 Liên hệ**: [vinh021104@gmail.com]  
**💬 Feedback**: Mọi góp ý để cải thiện chất lượng dịch thuật xin gửi về email trên  
**🔄 Updates**: Bài dịch sẽ được cập nhật dựa trên feedback từ cộng đồng

---

*© 2025 - Bản dịch thuộc về Ngô THành Vinh. Vui lòng credit khi sử dụng.*