# Generic Project

## Hierarchy
- document (root)
  - document_id
  - document_type
  - file_name
  - metadata
    - author
    - created_date
    - mime_type
  - pages (array)
    - page_number
    - sections (array)
      - section_id
      - title
      - bounding_box
      - text
      - elements (array)
        - element_type (heading | paragraph | table)
        - text
        - bounding_box
        - tag (for HTML)
        - rows (for tables)
          - row_number
          - cells (array)
            - text
            - bounding_box

document (root)
│
├── document_id
├── document_type
├── file_name
├── metadata
│   ├── author
│   ├── created_date
│   ├── mime_type
│
└── pages (array)
    ├── page_number
    └── sections (array)
        ├── section_id
        ├── title
        ├── bounding_box
        ├── text
        └── elements (array)
            ├── element_type (heading | paragraph | table)
            ├── text
            ├── bounding_box
            ├── tag (for HTML)
            ├── rows (for tables)
            │   └── row_number
            │       └── cells (array)
            │           ├── text
            │           └── bounding_box


{
  "document": {
    "document_id": "unique_id",
    "document_type": "pdf | html | docx",
    "file_name": "sample_file.pdf",
    "metadata": {
      "author": "Author Name",
      "created_date": "2024-10-06",
      "mime_type": "application/pdf | text/html | application/vnd.openxmlformats-officedocument.wordprocessingml.document"
    },
    "pages": [
      {
        "page_number": 1,
        "sections": [
          {
            "section_id": "sec1",
            "title": "Section Title",
            "bounding_box": { "x1": 0.1, "y1": 0.1, "x2": 0.9, "y2": 0.2 },
            "text": "Sample text from this section.",
            "elements": [
              {
                "element_type": "heading | paragraph | table",
                "text": "Sample text in heading or paragraph",
                "bounding_box": { "x1": 0.1, "y1": 0.3, "x2": 0.8, "y2": 0.4 },
                "tag": "h1 | p",  // For HTML
                "rows": [  // For tables
                  {
                    "row_number": 1,
                    "cells": [
                      {
                        "text": "Header 1",
                        "bounding_box": { "x1": 0.1, "y1": 0.5, "x2": 0.2, "y2": 0.6 }
                      },
                      {
                        "text": "Header 2",
                        "bounding_box": { "x1": 0.2, "y1": 0.5, "x2": 0.3, "y2": 0.6 }
                      }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
}

For chunking documents in a **Retrieval-Augmented Generation (RAG)** use case, choosing between **JSON** and **JSONL** depends on how you plan to process the chunks, store the data, and retrieve it for embedding and retrieval. Here’s a breakdown of the pros and cons of each in the context of chunking documents for RAG:

### JSON (Standard JSON)
**Pros**:
- **Better for Hierarchical Data**: JSON’s nested structure makes it ideal for maintaining the relationships between a document’s pages, sections, and content chunks. This can help preserve the logical structure when generating responses or retrieving information.
- **One File, One Structure**: The entire document hierarchy is encapsulated in one structured object, which can simplify querying or transforming the document as a whole for vectorizing sections or chunks.
- **Easier to Serialize/Deserialize**: JSON is widely supported, and the deserialization of the entire document into memory is typically straightforward in most languages, which is useful if you need to access the entire document for context.
  
**Cons**:
- **Memory-Heavy for Large Documents**: For very large documents (or large collections of documents), loading and processing the entire JSON file into memory can become inefficient, especially if you only need to process or retrieve parts of it.
- **Harder to Update Incrementally**: If you need to add new chunks or sections, the entire document needs to be re-serialized and saved, which can be inefficient in high-throughput or real-time processing environments.

### JSONL (JSON Lines)
**Pros**:
- **Efficient for Streaming and Incremental Processing**: JSONL is well-suited for RAG use cases where you need to process or embed chunks incrementally. Each chunk (or section) can be a separate JSON object, making it easier to load and process one chunk at a time.
- **Scalability**: JSONL allows for easier handling of large-scale datasets. You can process document chunks independently, which is beneficial for RAG when dealing with massive document stores and distributed systems.
- **Supports Distributed Systems**: JSONL is compatible with parallel and distributed processing frameworks, making it ideal for large-scale systems like document embedding pipelines where different workers process different chunks.
- **Append-Only for New Chunks**: JSONL files can be easily appended to, making it efficient to add new chunks without having to rewrite the entire document.

**Cons**:
- **Loses Hierarchical Readability**: JSONL is inherently flat and loses the hierarchical nature of documents. You’ll need to rely on additional metadata (like document IDs and section IDs) to preserve context across chunks, which can add complexity in ensuring proper document structure.
- **Complexity in Querying**: If you need to retrieve an entire section or reconstruct a document from its chunks, you need to aggregate multiple lines from the JSONL file, which could be more complex compared to JSON where everything is nested in one object.

### Which is Better for RAG?

#### Use JSONL If:
- You have **large documents** or a large collection of documents that need to be chunked for processing and retrieval.
- You plan to **process document chunks independently** (e.g., one chunk per API call, or for embedding each chunk separately).
- You need the ability to **incrementally add chunks** (e.g., if you're dynamically chunking documents and appending new content over time).
- You're working in a **distributed environment** or with a **large-scale RAG pipeline**, where multiple workers or systems are handling different chunks of the document at the same time.

#### Use JSON If:
- Your document structure is complex and maintaining a **clear hierarchy** between document, section, and chunk is crucial for your use case.
- You will typically load the **entire document into memory** for processing (e.g., embedding entire sections or documents rather than individual chunks).
- You need to ensure the **contextual relationships** between sections and chunks are preserved without extra effort.
- Your use case involves **smaller datasets** or **fewer documents**, where efficiency is not a major concern.

### Summary:
- **JSONL** is generally better suited for **large-scale RAG use cases** where documents are processed incrementally or in chunks. It offers flexibility for distributed systems and efficient handling of large datasets.
- **JSON** is better for **smaller datasets** or where maintaining the hierarchical relationships in a single, readable structure is important. If you need the entire document context to remain intact when processing or embedding, JSON is preferable.

Given the nature of RAG, where individual chunks are often retrieved and embedded independently, **JSONL** is likely the better choice for scalability and efficiency. However, if maintaining full document structure is critical (e.g., if you need to answer questions based on entire sections of documents), you might still lean towards **JSON** for its hierarchical clarity.


Whether **JSONL** or **JSON** is better depends on your specific use case. Let's compare them to help you decide which format would be more suitable for your scenario involving document processing with sections, chunks, and hierarchical data.

### JSON:
- **Better for Complex, Hierarchical Data**: JSON supports nested structures, which makes it ideal for representing complex hierarchical relationships like documents containing pages, sections, and content elements (titles, paragraphs, tables).
- **Easier to Read as a Single Entity**: JSON wraps all data into one structured object, making it easier to view and parse an entire document hierarchy at once. This is useful when you need to load or modify the entire structure at once.
- **Single Large Object**: JSON stores everything in one object, which could be less efficient if you are working with large documents where you want to access or process small parts independently.

### JSONL:
- **Better for Large Datasets and Streaming**: JSONL (JSON Lines) is better when you're dealing with large documents or data that can be processed incrementally, such as when you want to read or write data one section or chunk at a time. Each line is a separate JSON object, making it more memory-efficient.
- **Simpler for Append or Incremental Operations**: You can easily append new content (sections, pages, or chunks) by adding a new line to the JSONL file. It's easier to modify a part of the document without having to reload the entire file into memory.
- **Less Hierarchical Readability**: JSONL is less visually readable and can be harder to interpret as a single cohesive structure since it lacks explicit nested hierarchy unless manually reconstructed from related fields (e.g., `document_id`, `section_id`).

### Use Cases:
- **When to Use JSON**:
  - If your document structure is highly hierarchical and you need to keep everything in context (document → page → section → element).
  - When you need to work with the entire document at once, such as for generating reports or for rendering large sections in UI.
  - If you have smaller document sizes and need readability for manual inspection or debugging.

- **When to Use JSONL**:
  - If you're dealing with **large documents** where sections, pages, or content elements are independent, allowing for chunked or incremental processing.
  - When your workflow involves **streaming data** or processing one record at a time (for example, machine learning pipelines or distributed processing tasks).
  - If performance and memory efficiency are critical, such as when handling thousands of documents or multi-gigabyte datasets.

### Conclusion:
- **For complex, hierarchical document structures** (like the one you've described with document, page, section, and chunked elements), **JSON** would be better if you are primarily dealing with the entire document as a single entity. It preserves the hierarchy more intuitively.
- **For large datasets or incremental processing** where you only need to process small parts of the document at a time, **JSONL** is better, especially if you need to efficiently handle many sections or elements.

If you're working with huge datasets and want efficient processing, JSONL may be the better option. But if readability and structure are key and you're dealing with smaller sets of data, JSON will serve you better.


When considering **JSONL (JSON Lines)** versus **JSON Hierarchical Documents** for asynchronously ingesting data into a **VectorDB**, there are several important factors to take into account, especially in the context of **asynchronous ingestion** and the **ChatGPT use case**:

### JSONL (JSON Lines)

#### Advantages:
1. **Efficient for Streaming and Asynchronous Processing**:
   - **Streamlined Asynchronous Ingestion**: JSONL supports incremental or line-by-line ingestion, making it ideal for scenarios where data is processed and indexed asynchronously. Each line represents a separate document or chunk, which allows for partial ingestion without requiring the entire dataset to be loaded into memory at once.
   - **Partial Updates**: You can easily append or update individual chunks without having to reprocess the entire document. This flexibility is highly beneficial in use cases where data is received in real-time, such as asynchronous ChatGPT outputs.

2. **Parallel Processing**:
   - Each line in JSONL can be processed independently, allowing for **parallel processing** in distributed systems. This is particularly useful when ingesting into a VectorDB, as you can divide the workload across multiple workers, leading to faster indexing.

3. **Scalability**:
   - **Large Document Handling**: For large documents or datasets, JSONL is much more efficient because it reduces memory consumption. Each line is treated as a separate document or entity, which allows the ingestion pipeline to process large amounts of data without loading the full document tree into memory.
   - **Ideal for Chunking in RAG**: Since JSONL works well with individual records, it is perfect for scenarios where data needs to be chunked before being embedded into a VectorDB. In the **RAG (Retrieval-Augmented Generation)** context, text chunks can be indexed independently for vector-based retrieval.

4. **Error Isolation**:
   - In JSONL, since each line is a self-contained JSON object, it’s easier to isolate and fix errors in individual records without affecting the rest of the data. This ensures more robust ingestion in asynchronous or real-time systems.

5. **Simpler for Log-Like Data**:
   - When handling asynchronous ingestion like ChatGPT responses, JSONL is better suited for "log-like" or continuous streams of data, where each line is independent. You can process the chunks independently as they arrive.

#### Disadvantages:
1. **Less Readable for Humans**:
   - JSONL is harder to read in its raw form compared to a hierarchical JSON document, especially when inspecting or manually debugging complex data structures.

2. **Reconstruction Complexity**:
   - Since JSONL is flat, reconstructing hierarchical relationships can be more challenging, especially if chunks need to be reassembled in a certain order.

---

### JSON Hierarchical Documents

#### Advantages:
1. **Clear Data Structure**:
   - JSON hierarchical documents are **easy to read** and **self-contained**, making them ideal when you need to maintain complex relationships between nested elements. In a hierarchical JSON format, relationships between sections, chunks, or metadata are clear and easy to navigate.

2. **Atomic Document Ingestion**:
   - A hierarchical JSON document can be ingested as a whole entity, preserving its relationships. This is useful if each ingestion should capture the full context of the document or conversation, such as a complete ChatGPT dialogue session, where understanding requires the entire context.

3. **Good for Small, Static Datasets**:
   - Hierarchical JSON can be a good choice if the dataset is relatively small and doesn’t change often. It maintains its structure in a well-defined manner and is ideal if hierarchical relationships are important for retrieval.

#### Disadvantages:
1. **Inefficient for Streaming**:
   - **Memory Intensive**: Ingesting a hierarchical JSON document into a VectorDB would require loading the entire document into memory, making it inefficient for very large or streaming data scenarios.
   - **Not Well Suited for Asynchronous Data**: As new data (e.g., new ChatGPT responses) becomes available, it’s challenging to append or update parts of a hierarchical JSON document without loading the whole document. JSONL, by contrast, allows for easy appending.

2. **Not Ideal for Parallelism**:
   - JSON hierarchical documents are harder to parallelize since the entire document needs to be ingested at once. In contrast, JSONL allows you to divide the work and ingest chunks concurrently.

3. **Complexity with Chunking**:
   - In RAG use cases, hierarchical JSON is less optimal because it doesn’t easily lend itself to the chunking required for embedding text into a VectorDB. Chunking the data for embedding would require pre-processing to break the hierarchical structure into smaller, manageable pieces.

---

### Which Format is Better for Asynchronous Ingestion into a VectorDB?
Given your requirements for **asynchronous ingestion into a VectorDB** (likely for RAG or semantic search purposes) and the nature of the ChatGPT data (which can be processed in chunks), **JSONL is the better choice**.

#### Reasons:
1. **Asynchronous Nature**: JSONL's line-by-line structure makes it perfect for asynchronously ingesting data, especially as chunks are generated by ChatGPT over time.
2. **Chunking**: In a RAG use case, text is chunked and embedded into a VectorDB. JSONL simplifies this process since each line (or chunk) can be processed independently and inserted into the vector store in real-time.
3. **Scalability and Performance**: JSONL is more scalable and allows for parallel processing, meaning that as you ingest the data asynchronously, it can be processed in parallel by different workers, speeding up the indexing process.
4. **Error Isolation**: JSONL’s structure allows you to handle errors on a per-line basis without re-ingesting the entire document, making it more robust for asynchronous, real-time data ingestion.

In summary, **JSONL** is a better fit for **asynchronous ingestion into a VectorDB**, especially for scenarios involving ChatGPT responses where data arrives in chunks. Its scalability, streaming support, and parallel processing advantages make it highly suitable for the use case you're describing.


Here’s a list of services available in **Azure Document Intelligence**, **Google Cloud Document AI (GCP Doc AI)**, and **Unstructured.io** to extract unstructured content from PDF MIME types and output it in a JSON structure:

---

### **1. Azure Document Intelligence (formerly Form Recognizer)**

Azure offers several pre-trained and customizable services for processing PDFs and extracting structured or unstructured content. These services output JSON-based structured data.

- **Layout Model**:
  - Extracts text, tables, selection marks, and the layout of documents.
  - Best for unstructured content like PDFs with various layouts.
  
- **Prebuilt Models**:
  - **Invoice Model**: Extracts structured fields like dates, amounts, and vendor information from invoices.
  - **Receipt Model**: Extracts data from receipts such as vendor, total, and date.
  - **Identity Document Model**: Extracts information from identity documents such as passports and driver’s licenses.
  - **Business Card Model**: Extracts contact information from business cards.
  - **Tax Form Model**: Extracts key-value pairs from tax forms.

- **Custom Models**:
  - You can train custom models to extract data from specific fields or layouts in your PDFs.

---

### **2. Google Cloud Document AI (GCP Doc AI)**

Google’s Document AI suite offers several document processing tools designed to extract structured and unstructured data from various document types, including PDFs.

- **Document AI** (General Processor):
  - Extracts text, layout, entities, and tables from general documents like PDFs, Word documents, etc.
  
- **Form Parser**:
  - Designed to extract form fields, tables, and key-value pairs from documents like PDFs and scanned images.
  
- **Invoice Parser**:
  - Extracts invoice-specific information like amounts, dates, vendors, and items.

- **Expense Parser**:
  - Extracts expenses from receipts and invoices, structured as JSON objects.

- **Identity Parser**:
  - Extracts data from identity documents such as passports and licenses.

- **Table Parser**:
  - Extracts and structures tabular data from documents like spreadsheets or PDF tables.

- **Custom Document AI**:
  - You can create and train custom parsers to suit your specific needs for documents with unique structures.

---

### **3. Unstructured.io**

**Unstructured.io** provides open-source and API-based solutions to extract structured data from a variety of document types, including PDFs. The services automatically handle mixed content types like tables, paragraphs, images, and lists, and output structured JSON.

- **Unstructured API**:
  - This API parses PDFs and extracts the document's structure into JSON format. It handles unstructured elements like headings, paragraphs, tables, and figures.
  
- **Partitioning Libraries**:
  - These libraries break documents into smaller components (chunks) such as text, metadata, and visual elements, and can be used to handle complex PDFs.
  
- **Integration with VectorDBs**:
  - Unstructured.io can integrate with VectorDB solutions by chunking and processing PDFs for storage and retrieval in a JSON structure.

---

### Summary:

- **Azure Document Intelligence**: Layout Model, Prebuilt Models (Invoice, Receipt, Identity, etc.), Custom Models.
- **GCP Document AI**: General Processor, Form Parser, Invoice Parser, Expense Parser, Identity Parser, Table Parser, Custom Models.
- **Unstructured.io**: API for general unstructured document extraction, partitioning libraries for chunking complex documents.

These services all provide powerful extraction of unstructured content from PDFs and output it in a JSON format. Let me know if you need more specific examples or configurations for any of these services!


Here’s a **table format** comparing the services offered by **Azure Document Intelligence**, **Google Cloud Document AI (GCP Doc AI)**, and **Unstructured.io** for extracting unstructured content from PDFs and outputting it in a JSON format:

| **Vendor**                       | **Service Name**                   | **Description**                                                                                              | **Best for**                                      |
|-----------------------------------|-------------------------------------|--------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| **Azure Document Intelligence**   | Layout Model                        | Extracts text, layout, tables, and selection marks from documents.                                             | PDFs with various layouts, tables, and forms      |
|                                   | Invoice Model                       | Prebuilt model for extracting structured fields like dates, vendors, and totals from invoices.                 | Invoice PDFs                                      |
|                                   | Receipt Model                       | Extracts data like vendor, total amount, and date from receipts.                                               | Receipts                                          |
|                                   | Identity Document Model             | Extracts information from identity documents such as passports and driver's licenses.                          | Passports, licenses                               |
|                                   | Business Card Model                 | Extracts contact information from business cards.                                                             | Business cards                                    |
|                                   | Tax Form Model                      | Extracts key-value pairs from tax forms.                                                                      | Tax forms                                         |
|                                   | Custom Model                        | Train a custom model to extract data from specific fields or layouts in unstructured documents.                | Unique document types                             |
|                                   | Table Extraction                    | Detects and extracts structured tables from documents.                                                        | PDFs with tables                                  |
|                                   | Key-Value Extraction                | Extracts key-value pairs from unstructured documents.                                                         | Unstructured PDFs                                 |

| **Google Cloud Document AI (GCP)**| General Processor                   | Extracts text, layout, entities, and tables from general documents.                                            | General PDFs                                      |
|                                   | Form Parser                         | Extracts form fields, tables, and key-value pairs from documents like PDFs and scanned images.                 | Forms, surveys                                    |
|                                   | Invoice Parser                      | Extracts invoice-specific data like amounts, dates, vendors, and line items.                                   | Invoice PDFs                                      |
|                                   | Expense Parser                      | Extracts expense-related data such as amounts, vendors, and dates from receipts and invoices.                  | Expense receipts, invoices                        |
|                                   | Identity Parser                     | Extracts data from identity documents like passports, licenses, and ID cards.                                  | Identity documents                                |
|                                   | Table Parser                        | Detects and extracts tabular data from documents such as spreadsheets and PDFs.                                | Tabular PDFs                                      |
|                                   | Custom Document AI                  | Train a custom parser to extract content from specific document types.                                         | Custom document types                             |

| **Unstructured.io**               | Unstructured API                    | General API to parse and extract structured content from unstructured PDFs, including text, headings, and tables.| General PDFs with mixed content                  |
|                                   | Partitioning Libraries              | Libraries to break documents into chunks, such as text, metadata, and tables for easy processing.              | Complex unstructured documents                    |
|                                   | Table Extraction                    | Extracts and organizes table data from PDFs into structured formats.                                           | PDFs with tables                                  |
|                                   | Integration with VectorDBs           | Integrates with VectorDB solutions to chunk, process, and store documents in JSON format.                      | Chunking and storing unstructured documents       |

### Summary:
- **Azure Document Intelligence**: Strong for pre-built models (invoices, receipts, identity documents) and offers layout-based extraction.
- **Google Cloud Document AI**: Versatile with general-purpose parsers and specialized models (invoices, forms, identities), with options to train custom models.
- **Unstructured.io**: Great for handling mixed content in PDFs (text, tables, images) with a strong focus on chunking and partitioning for document processing.

Let me know if you need further clarification on any specific service!


Here’s a table specifically for **GCP Document AI (Doc AI)** services, showing the service name, description, and the best use cases:

| **Service Name**                  | **Description**                                                                                              | **Best For**                                      |
|-----------------------------------|--------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| **General Processor**             | Extracts text, layout, entities, and tables from general documents like PDFs and Word documents.              | PDFs, DOCX, general document processing            |
| **Form Parser**                   | Extracts form fields, tables, and key-value pairs from documents such as forms and surveys.                   | Forms (PDF, scanned forms), surveys                |
| **Invoice Parser**                | Extracts invoice-specific fields like amounts, dates, vendor details, and line items.                         | Invoice PDFs, scanned images                       |
| **Expense Parser**                | Extracts expense-related data from receipts and invoices, including totals, dates, and merchant information.   | Expense receipts, invoices                         |
| **Identity Parser**               | Extracts key details from identity documents such as passports, driver’s licenses, and ID cards.              | Identity documents (passports, licenses, ID cards) |
| **Table Parser**                  | Detects and extracts tabular data from documents, including rows, columns, and headers.                       | Tables in PDFs, DOCX, spreadsheets                 |
| **Custom Document AI**            | Enables users to train custom models to extract specific information from unique or structured documents.      | Custom document types, specialized document layouts|

### Summary of GCP Document AI:
- **General Processor**: Best for extracting text, layout, and tables from common document types like PDFs and DOCX.
- **Form Parser**: Specifically designed to extract data from forms and surveys.
- **Invoice Parser & Expense Parser**: Specialized for financial documents like invoices and receipts.
- **Identity Parser**: Useful for extracting structured data from identity documents like passports and licenses.
- **Table Parser**: Extracts structured tabular data from various types of documents.
- **Custom Document AI**: Allows users to train models to suit specific document structures and use cases.

Let me know if you need more details or further comparisons!


Yes, both **Azure Document Intelligence (Layout Model)** and **GCP Document AI (General Processor)** provide their outputs in **JSON format**. The JSON output contains detailed information about the content and structure of the documents, including extracted text, tables, key-value pairs, and more.

### 1. **Azure Document Intelligence (Layout Model)**:
   - **Output Format**: JSON
   - **Details Extracted**:
     - **Text**: Extracted text from the document, including paragraphs, headers, and individual lines.
     - **Tables**: Structured tables with rows and cells, including text content and position on the page.
     - **Selection Marks**: Elements like checkboxes or radio buttons.
     - **Bounding Boxes**: Coordinates of the extracted elements (for layout preservation).
     - **Confidence Scores**: Each extracted element includes a confidence score indicating how certain the model is about the accuracy.

   **Sample Output (Azure Layout Model)**:
   ```json
   {
     "document": {
       "pages": [
         {
           "pageNumber": 1,
           "dimensions": {
             "width": 8.5,
             "height": 11.0,
             "unit": "inch"
           },
           "paragraphs": [
             {
               "boundingBox": {
                 "vertices": [
                   { "x": 45, "y": 90 },
                   { "x": 450, "y": 90 },
                   { "x": 450, "y": 120 },
                   { "x": 45, "y": 120 }
                 ]
               },
               "text": "This is a sample paragraph extracted from the PDF.",
               "confidence": 0.99
             }
           ],
           "tables": [
             {
               "rows": [
                 {
                   "cells": [
                     { "text": "Header 1" },
                     { "text": "Header 2" }
                   ]
                 },
                 {
                   "cells": [
                     { "text": "Data 1" },
                     { "text": "Data 2" }
                   ]
                 }
               ]
             }
           ]
         }
       ]
     }
   }
   ```

### 2. **GCP Document AI (General Processor)**:
   - **Output Format**: JSON
   - **Details Extracted**:
     - **Text**: Full document text, split into logical elements like paragraphs, sentences, and tokens.
     - **Entities**: Identified entities such as names, dates, and amounts.
     - **Tables**: Extracted tables with structured rows, columns, and text.
     - **Bounding Boxes**: For layout-based documents (e.g., PDFs), bounding boxes are provided for each element (text, tables, images).
     - **Confidence Scores**: Each element has an associated confidence score.

   **Sample Output (GCP Document AI General Processor)**:
   ```json
   {
     "document": {
       "text": "This is the extracted text from the document.",
       "pages": [
         {
           "pageNumber": 1,
           "dimensions": {
             "width": 8.5,
             "height": 11.0
           },
           "paragraphs": [
             {
               "boundingBox": {
                 "vertices": [
                   { "x": 0.1, "y": 0.1 },
                   { "x": 0.9, "y": 0.1 },
                   { "x": 0.9, "y": 0.2 },
                   { "x": 0.1, "y": 0.2 }
                 ]
               },
               "text": "This is a paragraph of text from the document.",
               "confidence": 0.98
             }
           ],
           "tables": [
             {
               "headerRows": [
                 {
                   "cells": [
                     { "text": "Header 1" },
                     { "text": "Header 2" }
                   ]
                 }
               ],
               "bodyRows": [
                 {
                   "cells": [
                     { "text": "Data 1" },
                     { "text": "Data 2" }
                   ]
                 }
               ]
             }
           ]
         }
       ]
     }
   }
   ```

### Summary of Outputs:
- **Both Azure and GCP services provide structured JSON output**, which includes text, tables, bounding boxes, and other relevant data extracted from the document.
- **Bounding Boxes**: Both services include bounding boxes for layout-based documents (PDFs), helping to preserve the spatial layout.
- **Tables and Paragraphs**: They both handle text, tables, and other structural elements well, making it easy to work with different document types.

This **JSON output** is ideal for further processing, indexing, embedding, or rendering in other applications.

Let me know if you need further customization or help with testing these services!