## Python script is to extract image files from a specified directory within a ZIP archive and upload them to an Amazon S3 bucket. The script ensures that each file is checked for existence in the bucket before uploading, handles potential errors with retries, and performs the uploads concurrently for efficiency.

## This code ensures that only images within the specified path in the ZIP archive are uploaded to S3, with proper error handling and logging to track the process. Using concurrent uploads and streams helps manage large files and improve performance.
### Complete script 
```
import boto3
import zipfile
import mimetypes
import logging
import concurrent.futures
from botocore.exceptions import ClientError, EndpointConnectionError
from time import sleep
import io

# Initialize the S3 client with increased connection pool size
session = boto3.Session()
s3_client = session.client('s3', config=boto3.session.Config(max_pool_connections=100))

# Configure logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s %(levelname)s: %(message)s')

# Function to check if an object exists in S3
def object_exists(bucket_name, object_key):
    retries = 5
    for attempt in range(retries):
        try:
            s3_client.head_object(Bucket=bucket_name, Key=object_key)
            return True
        except ClientError as e:
            if e.response['Error']['Code'] == '404':
                return False
            else:
                logging.error(f"Error checking if object exists: {e}")
                if attempt < retries - 1:
                    sleep(2 ** attempt)
                else:
                    raise
        except EndpointConnectionError as e:
            logging.error(f"Endpoint connection error: {e}")
            if attempt < retries - 1:
                sleep(2 ** attempt)
            else:
                raise

# Function to upload a file to S3
def upload_file_to_s3(bucket_name, file_name, file_data, content_type):
    retries = 5
    for attempt in range(retries):
        try:
            s3_client.put_object(
                Bucket=bucket_name, 
                Key=file_name, 
                Body=file_data,
                ContentType=content_type
            )
            logging.info(f"Uploaded {file_name} to s3://{bucket_name}/{file_name} with Content-Type {content_type}")
            return
        except ClientError as e:
            logging.error(f"Error uploading {file_name}: {e}")
            if attempt < retries - 1:
                sleep(2 ** attempt)  # Exponential backoff
            else:
                logging.error(f"Failed to upload {file_name} after {retries} attempts")
                raise

# Function to check if the file is an image
def is_image_file(file_name):
    mime_type, _ = mimetypes.guess_type(file_name)
    return mime_type and mime_type.startswith('image')

# Function to extract and upload files to S3
def extract_and_upload_to_s3(zip_file_path, bucket_name, extract_path):
    # Open the zip file in binary read mode
    with open(zip_file_path, 'rb') as zip_file:
        with zipfile.ZipFile(zip_file, 'r') as zip_ref:
            # Use ThreadPoolExecutor for concurrent uploads
            with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
                future_to_file = {}
                for file_name in zip_ref.namelist():
                    # Check if the file is inside the specified extract path and is an image
                    if file_name.startswith(extract_path) and not file_name.endswith('/') and is_image_file(file_name):
                        # Adjust the S3 key to remove the extract_path prefix
                        s3_key = file_name[len(extract_path):]

                        # Check if the object already exists in S3
                        if object_exists(bucket_name, s3_key):
                            logging.info(f"Skipped {file_name}: already exists in s3://{bucket_name}/{s3_key}")
                            continue

                        # Read the file's contents as a stream
                        with zip_ref.open(file_name) as file_data_stream:
                            file_data = io.BytesIO(file_data_stream.read())

                        # Determine the content type
                        content_type, _ = mimetypes.guess_type(file_name)
                        if not content_type:
                            content_type = 'binary/octet-stream'

                        # Submit the upload task to the executor
                        future = executor.submit(upload_file_to_s3, bucket_name, s3_key, file_data, content_type)
                        future_to_file[future] = file_name

                # Wait for all futures to complete
                for future in concurrent.futures.as_completed(future_to_file):
                    file_name = future_to_file[future]
                    try:
                        future.result()
                    except Exception as e:
                        logging.error(f"Error processing file {file_name}: {e}")

# Example usage
local_zip_file = r'C:\Users\truEq\Downloads\abc.zip'  # Path to your local zip file
s3_bucket_name = 'test-bucket'  # Your S3 bucket name
extract_path = 'abc/'  # The path inside the zip file to extract

extract_and_upload_to_s3(local_zip_file, s3_bucket_name, extract_path)

```

### Explaination :

Imports and Initialization
```
import boto3
import zipfile
import mimetypes
import logging
import concurrent.futures
from botocore.exceptions import ClientError, EndpointConnectionError
from time import sleep
import io
```
- **boto3**: AWS SDK for Python to interact with AWS services, including S3.
- **zipfile**: Python module for reading and writing ZIP files.
- **mimetypes**: Used to guess the MIME type of a file based on its name or URL.
- **logging**: Provides a flexible framework for emitting log messages.
- **concurrent.futures**: Module for asynchronous execution of callables.
- **ClientError, EndpointConnectionError**: Exceptions from boto3.
- **sleep**: Function to pause execution for a specified amount of time.
- **io**: Module for working with streams of data.

S3 Client Initialization
```
session = boto3.Session()
s3_client = session.client('s3', config=boto3.session.Config(max_pool_connections=100))
```
- **session**: Initializes a new session with boto3.
- **s3_client**: Creates an S3 client with an increased connection pool size for better performance during concurrent uploads.

Logging Configuration
```
logging.basicConfig(level=logging.INFO, format='%(asctime)s %(levelname)s: %(message)s')
```
- **logging.basicConfig**: Configures the logging module to display log messages with timestamps and severity levels.

Check if Object Exists in S3
```
def object_exists(bucket_name, object_key):
    retries = 5
    for attempt in range(retries):
        try:
            s3_client.head_object(Bucket=bucket_name, Key=object_key)
            return True
        except ClientError as e:
            if e.response['Error']['Code'] == '404':
                return False
            else:
                logging.error(f"Error checking if object exists: {e}")
                if attempt < retries - 1:
                    sleep(2 ** attempt)
                else:
                    raise
        except EndpointConnectionError as e:
            logging.error(f"Endpoint connection error: {e}")
            if attempt < retries - 1:
                sleep(2 ** attempt)
            else:
                raise
```
- **object_exists**: Checks if an object already exists in the S3 bucket.
- **retries**: Number of retries for handling transient errors.
- **sleep(2 ** attempt)**: Exponential backoff strategy to handle transient errors.

Upload File to S3
```
def upload_file_to_s3(bucket_name, file_name, file_data, content_type):
    retries = 5
    for attempt in range(retries):
        try:
            s3_client.put_object(
                Bucket=bucket_name, 
                Key=file_name, 
                Body=file_data,
                ContentType=content_type
            )
            logging.info(f"Uploaded {file_name} to s3://{bucket_name}/{file_name} with Content-Type {content_type}")
            return
        except ClientError as e:
            logging.error(f"Error uploading {file_name}: {e}")
            if attempt < retries - 1:
                sleep(2 ** attempt)  # Exponential backoff
            else:
                logging.error(f"Failed to upload {file_name} after {retries} attempts")
                raise
```
- **upload_file_to_s3**: Uploads a file to the specified S3 bucket.
- **put_object**: boto3 function to upload data to an S3 bucket.
- **Body**: The content of the file being uploaded.
- **ContentType**: MIME type of the file.

Check if File is an Image
```
def is_image_file(file_name):
    mime_type, _ = mimetypes.guess_type(file_name)
    return mime_type and mime_type.startswith('image')
```
- **is_image_file**: Determines if the given file is an image based on its MIME type.

Extract and Upload Files to S3
```
def extract_and_upload_to_s3(zip_file_path, bucket_name, extract_path):
    # Open the zip file in binary read mode
    with open(zip_file_path, 'rb') as zip_file:
        with zipfile.ZipFile(zip_file, 'r') as zip_ref:
            # Use ThreadPoolExecutor for concurrent uploads
            with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
                future_to_file = {}
                for file_name in zip_ref.namelist():
                    # Check if the file is inside the specified extract path and is an image
                    if file_name.startswith(extract_path) and not file_name.endswith('/') and is_image_file(file_name):
                        # Adjust the S3 key to remove the extract_path prefix
                        s3_key = file_name[len(extract_path):]

                        # Check if the object already exists in S3
                        if object_exists(bucket_name, s3_key):
                            logging.info(f"Skipped {file_name}: already exists in s3://{bucket_name}/{s3_key}")
                            continue

                        # Read the file's contents as a stream
                        with zip_ref.open(file_name) as file_data_stream:
                            file_data = io.BytesIO(file_data_stream.read())

                        # Determine the content type
                        content_type, _ = mimetypes.guess_type(file_name)
                        if not content_type:
                            content_type = 'binary/octet-stream'

                        # Submit the upload task to the executor
                        future = executor.submit(upload_file_to_s3, bucket_name, s3_key, file_data, content_type)
                        future_to_file[future] = file_name

                # Wait for all futures to complete
                for future in concurrent.futures.as_completed(future_to_file):
                    file_name = future_to_file[future]
                    try:
                        future.result()
                    except Exception as e:
                        logging.error(f"Error processing file {file_name}: {e}")

```
- **extract_and_upload_to_s3**: Extracts files from a ZIP archive and uploads them to an S3 bucket.
- **open(zip_file_path, 'rb')**: Opens the ZIP file in binary read mode.
- **zipfile.ZipFile**: Provides access to members of the ZIP archive.
- **ThreadPoolExecutor**: Manages a pool of threads for concurrent execution.
- **future_to_file**: Dictionary to map futures to file names for tracking.
- **file_name.startswith(extract_path)**: Ensures only files within the specified path are processed.
- **file_name[len(extract_path):]**: Adjusts the S3 key to remove the prefix path.
- **zip_ref.open(file_name)**: Opens a file in the ZIP archive as a stream.
- **io.BytesIO(file_data_stream.read())**: Reads the file data into a stream to handle large files efficiently.
- **executor.submit**: Submits a task to the thread pool for concurrent execution.
- **concurrent.futures.as_completed**: Iterates over completed futures.

Example Usage
```
local_zip_file = r'C:\Users\truEq\Downloads\beneficiary-geotag-images.zip'  # Path to your local zip file
s3_bucket_name = 'assetgeotag-mobileapp'  # Your S3 bucket name
extract_path = 'beneficiary-geotag-images/'  # The path inside the zip file to extract

extract_and_upload_to_s3(local_zip_file, s3_bucket_name, extract_path)
```
- **local_zip_file**: Path to the local ZIP file.
- **s3_bucket_name**: Name of the S3 bucket to upload files to.
- **extract_path**: The path within the ZIP file to extract files from.



