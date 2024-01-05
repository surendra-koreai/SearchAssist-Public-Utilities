# Evaluation Utility

The introduction summarizes the purpose and function of the project, and should be concise (a brief paragraph or two). This introduction may be the same as the first paragraph on the project page.

For a full description of the module, visit the
[project page](https://www.drupal.org/project/admin_menu).

Submit bug reports and feature suggestions, or track changes in the
[issue queue](https://www.drupal.org/project/issues/admin_menu).

## Table of contents

- Requirements
- Recommended modules
- Installation
- Configuration
- Troubleshooting
- FAQ
- Maintainers

## Requirements

This module requires the following modules:

- [Bad judgement](https://www.drupal.org/project/bad_judgement)

OR

This module requires no modules outside of Drupal core.

## Recommended modules (optional)

## Installation (required, unless a separate INSTALL.md is provided)

Install as you would normally install a contributed Drupal module. For further information, see [Installing Drupal Modules](https://www.drupal.org/docs/extending-drupal/installing-drupal-modules).

## Configuration (required)

1. Enable the module at Administration > Extend.
2. Profit.

## Troubleshooting (optional)

## FAQ (optional)

**Q: How do I write a module README?**

**A:** Follow this template. It's fun and easy!

## Maintainers

- Aakanksh Dudam - [Aakansh](https://www.drupal.org/u/dries)



This utility provides the detailed result about Searchassist accuracy. It generates a CSV file as an output with all the necessary debugging details. 
We do the comparison of our expected answer with retrived answer by 4 stages:  
1. Exact string match. 
2. Substring match 
3. Fuzzy strng match 
4. Semantic match by embeddings (This is useful for capturing whether the meaning is matched or not, but has a dependency on a python service, which needs to be run separately) 
- if you want to disable the Semantic match then keep the flag as semantic_flag = 0 in
the generation_utility.js code -if you want to use then keep the flag as
semantic_flag = 1

First, Starting embeddings_server (python) \[Only if we want to do a semantic match between expected and actual answer.For the semantic serach please place the model ie. file path of the multi-qa-mpnet-base-cos-v1 in the embeddings_server.py for the model_path.
**ex:** model_path =\"/var/www/multi-qa-mpnet-base-cos-v1\"

=\>**step1:** cd embeddings_server 
=\>**step2:** Install Python 3.8.10:
### How to install Python in ubuntu:
By following the steps in the below link :`<br />`
  [https://linuxize.com/post/how-to-install-python-3-8-on-ubuntu-18-04/](https://linuxize.com/post/how-to-install-python-3-8-on-ubuntu-18-04/) `<br />`

 - cd /data/  
 - wget
https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tgz  
- tar -xf Python-3.8.10.tgz  
- cd Python-3.8.10 ./configure\--enable-optimizations  
- make -j 8  
- sudo make altinstall

**Create and Activate virtual environment:**

 - cd /path/to/your_folder  
 - python3.8 -m venv venv_name  
 - source venv_name/bin/activate

**import all the required packages:** 
- pip install Flask 
- pip install numpy
- pip install nltk 
- pip install sentence-transformers

=\>**step3:** run python3 embeddings_server.py

Secondly, Making all the app and input CSV details available by running the server.js in the file_read_server folder by

=\>**step1:** cd file_read_server

=\>**step2:** make the changes for the credentials with respective to the search-assist application  
- **host:** ex:\"https://searchassist-app.kore.ai\"

 - **stream_id:** stream_id is nothing but the app id in the channels in our search-assist application.
**ex:**\"st-------------5ce3ca2e8ec5\"

 - **search_index_id:** search_index_id can also be found in the channels in our search-assist application.
**ex:**\"sidx-----------f0240afc9ec2\"

 - **(index_pipeline_id, query_pipeline_id, user_id):**
 You can get the above three credentials through inspect in the broswer, from our application click on the \'Serach Interface\' tab in the inspect go to network tab and click on the serachInterface api call, on to the right side goto preview where you can see the index_pipeline_id, search_index_id, and user_id is nothing but createdBy in the preview.
**ex:** 
**index_pipeline_id =** \"fip--------85ed98142f8c\"
**query_pipeline_id =** \"fqp--------2773cb6eeff4\" 
**user_id=** \"u--997c---------7a08\"

 - **auth_token:** you can get the bearer toke in the headers of the serachInterface api call itself. 
 **ex:** \"bearer ----------------x5Bj_jHTCzT8e1bLYk----------\"
(Note:The bearer Token is refreshed for every 24 hrs so place the updated Bearer Token)

=\>**step3:** install the required modules by running the below commands:
- npm install express 
- npm install body-parser 
- npm install fs 
- npm install fast-csv

=\>**step4:** run node credentials_and_readcsv.js

Then next, we run the actual script that makes SA calls and generates the result sheet by runnning the final_end.js from the Utility folder
=\>**step1:** cd Utility 
=\>**step2:** install the required modules by running the below commands:
- npm install fs 
- npm install fast-csv 
- npm install axios
- npm install path 
- npm install express 
- npm install fuzzball 
- npm install compute-cosine-similarity 
- npm install csv-writer 

=\>**step3:** run node generation_utility.js

**The format of the query.csv file:** 
- It can include ID, User_Quer, expected_url, expected_ans,expected_title although it is not mandatory to include ID and expected_url & expected_title, but mandatory to include the User_Query and expected_ans.
- The format of the result.csv file: 
- It has 13 columns which are:  
- **ID:** The Number of the query.  
- **User_Query:** The user query for the searchassist application.  
- **Expected_Ans:** The user expected answer for the query.  
- **Expected_URl:** The user expected Url for the query.  
- **Expected_title:** The user expected Title for the query.  
- **Retrived_ans:** The answer that our search assist application produced based on ingested data.  
- **Retrived_Urls:** The Url of the retrived answer.  
- **Answer_Snippet_Status:** If our expected answer is matched with our retrived ans then it will be true, else if it is not matched then false.
- **Url_Status:** If our expected url is matched with our retrived url then it will be true, else if it is not matched then false.  
- **Title_Status:** It gives the title status as true if expected title and retrived title
matched,or else false(but only works for extractive model, generative
empty status).  
- **Chunk_Position:** It gives the expected_ans chunk_position from the retrived chunks.  
- **Model_Used:** It tells about the model used. whether it is an extractive/generative model.  
- **Debug_Response:** it gives the lsit of retrived chunks. Bydefault it gives
lsit of retrived chunks, but to see the actual retrived chunks 
{
//\'debug_console\':chunks (uncomment this line in the generation_utility) 
\'debug_console\': \'lsit of retrived chunks\' (comment this line in the generation_utility)
}

For your reference both the Query csv and Result csv sheets are added. Please refer it if necessary.