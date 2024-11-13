# PDF Table Information Extraction with Azure OpenAI  
   
## Overview  
   
This repository contains a Python script that processes PDF documents by extracting images from each page and using Azure OpenAI services to extract specific information from the images.  
   
The script performs the following tasks:  
   
- Reads PDF files from the `input_documents` directory.  
- Iterates over every page in each PDF document.  
- Converts each page into an image and saves it in the `output_images` directory.  
- Sends the image along with a prompt to Azure OpenAI to extract structured information.  
- Saves the extracted information as JSON files in the `output_results` directory.  
- Prints the image size and dimensions for each processed page.  
   
## Table of Contents  
   
- [Requirements](#requirements)  
- [Setup](#setup)  
  - [Clone the Repository](#clone-the-repository)  
  - [Install Dependencies](#install-dependencies)  
  - [Set Up Azure OpenAI](#set-up-azure-openai)  
  - [Configure Environment Variables](#configure-environment-variables)  
  - [Prepare Input Documents](#prepare-input-documents)  
- [Usage](#usage)  
- [Example Output](#example-output)  
- [Customization](#customization)  
- [Notes](#notes)  
- [License](#license)  
   
## Requirements  
   
- Python 3.7 or higher  
- An Azure OpenAI account with appropriate API access  
- The following Python packages:  
  - `openai`  
  - `PyMuPDF` (also known as `fitz`)  
  - `pydantic`  
  - `python-dotenv`  
   
## Setup  
   
### Clone the Repository  
   
```bash  
git clone https://github.com/miguelmsft/analyze-documents-tables.git 
cd yourrepository  
```  
   
### Install Dependencies  
    
```bash  
pip install openai PyMuPDF pydantic python-dotenv  
```  
   
### Set Up Azure OpenAI  
   
1. **Create an Azure OpenAI Resource**:  
  
   - Sign in to the [Azure Portal](https://portal.azure.com/).  
   - Create an Azure OpenAI resource.  
   - Deploy the desired model and note the deployment name.  
   
2. **Obtain API Credentials**:  
  
   - Navigate to the Azure OpenAI resource you created.  
   - Go to the **Keys and Endpoint** section.  
   - Copy the **Endpoint** and one of the **Keys**.  
   
### Configure Environment Variables  
   
Create a `.env` file in the root directory of the repository to store your Azure OpenAI credentials:  
   
```bash  
touch .env  
```  
   
Add the following environment variables to the `.env` file:  
   
```bash  
AOAI_ENDPOINT=https://your-azure-openai-endpoint  
AOAI_API_KEY=your-azure-openai-api-key  
AOAI_DEPLOYMENT=your-deployment-name  
```  
   
- Replace `https://your-azure-openai-endpoint` with your actual Azure OpenAI endpoint.  
- Replace `your-azure-openai-api-key` with your Azure OpenAI API key.  
- Replace `your-deployment-name` with the name of your model deployment.  
   
### Prepare Input Documents  
   
Create an `input_documents` directory and place your PDF files there:  
   
```bash  
mkdir input_documents  
```  
   
Copy your PDF documents into the `input_documents` directory.  
   
## Usage  
   
Run the script using the following command:  
   
```bash  
python analyze_images.py  
```  
   
There are 3 different variations of the script:
- `analyze_images_general.py`: Extract table information from a prompt description.
- `analyze_images_dict.py`: Extract table information from a prompt description and a general dictionary template.
- `analyze_images_static.py`: Extract table information from a defined table schema. Use this if you know beforehand the table definition.
   
The script will process each PDF file in the `input_documents` directory and output the results to:  
   
- `output_images`: Contains the images extracted from each PDF page.  
- `output_results`: Contains the JSON files with the extracted information.  
   
## Example Output  
   
When you run the script, you will see output similar to the following:  
   
```  
Processing dummy_document.pdf...  
Saved image to dummy_document_page1.png  
Image size: 374934 bytes  
Image dimensions: 1224x1584  
Saved extracted information to dummy_document_page1.json  
Saved image to dummy_document_page2.png  
Image size: 380210 bytes  
Image dimensions: 1224x1584  
Saved extracted information to dummy_document_page2.json  
...  
```  
   
This indicates that the script is processing each page of the PDF, saving the images, printing their sizes and dimensions, and saving the extracted information.  
   
## Customization  
   
### Modifying the Prompt  
   
The `prompt` variable in the script defines what information you want to extract from each page:  
   
```python  
prompt = """Based on this image, extract the following information:  
            1) The names of the wells. If there are none, write "NA".  
            2) Is there a table in this page? Answer yes or no.  
            3) If there is a table, is it a cost code table? A cost code table should have cost codes, cost code descriptions, and cost values in USD. Answer yes or no.  
            3) Every row in a cost code table. If there is no table, write "NA". If the table is not related to cost codes, write "Not a cost code table". If there is a cost code table, every row of the table will be an item in the output list. Every list item will contain a dictionary where the dictionary key is the column name, and the dictionary value is the value for that row. The code code table will include a column for the cost code (for example, 191-026), a column for the description (for example, "WATER AND TRANSFER"), and one or more columns for the cost(it could be, for example, dry hole cost, completion cost, total cost, etc) and the value in USD (for example, $1,000,000).  
            """  
```  
   
You can modify this prompt to suit your specific use case.  
   
### Adjusting the Output Format  
   
The `ExtractedInformation` class defines the structure of the extracted data:  
   
```python  
class ExtractedInformation(BaseModel):  
    wellsNames: list[str]  
    tableFound: str  
    costCodeTableFound: str  
    costCodeTable: list[str]  
```  
   
You can modify this class to change the expected output schema. Ensure that the properties match what you are asking for in the prompt.  
   
## License  
   
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.