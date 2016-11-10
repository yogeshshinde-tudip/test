Pre-requisites:
Ruby >= 2.1

# Project Configuration

 1. Git clone using command

    * git clone https://git.tudip.com/ruby-rmagick/api.git

 2. Install gems using bundle install.

 3. Database configurations:

    * Update config/aws_dynamo.yml' to add AWS credentials.

    * Add SQS URL and S3 bucket name in the environment.rb

    * Update table names and post request URL to get the decryption key in the global_constants.rb.

    * Add file_name as a primary key for all tables.

    * The database connection is added in the lib/aws.rb

# Project Flow

 1. Start rake task 'rake aws_sqs:fetch_sqs_messages' for polling SQS messages.

 2. It will hit manipulate action from file_processing module.

 3. Download file from the URL provided in the request to public/files directory.

 4. Decryption:
    * Make a post request using send_post_request action with a username and file_name to get the decryption key.
    * Decrypt downloaded file using decryption key and return json parsed data.

 5. Preprocessing:
    * The request will be dumped in the request-dump table and image attributes in the All-Faces table.

 6. Run algorithms:
    * 3 algorithms will be executed using run_all_algorithms from the algorithm module.
    * Provide algorithm files path in the run_all_algorithms to execute algorithms.
    * Each algorithm will return 0 or -1 as success and error respectively.
    * If algorithm returns success, then the next algorithm will be executed.

 7. Postprocessing:
    * In post processing if action run_all_algorithms returns success and if it is a first request then parameters will be written in the json file and uploaded in the S3 buckets.
    * Image attributes will be stored in a Detected-Faces table on DynamoDB.
