# Table of Contents

1. [Challenge Summary] (README.md#challenge-summary)
2. [Details of Implementation] (README.md#details-of-implementation)
3. [Description of Data] (README.md#description-of-data)
4. [Writing clean, scalable and well-tested code](README.md#writing-clean-scalable-and-well-tested-code)
5. [Repo directory structure] (README.md#repo-directory-structure)
6. [Testing your directory structure and output format] (README.md#testing-your-directory-structure-and-output-format)
7. [FAQ] (README.md#faq)

##Challenge Summary

Imagine you're a data engineer at a "digital wallet" company called PayMo that allows users to easily request and make payments to other PayMo users. The team at PayMo has decided they want to implement features to prevent fraudulent payment requests from untrusted users. 

###Feature 1
When anyone makes a payment to another user, they'll be notified if they've never made a transaction with that user before.

* "unverified: You've never had a transaction with this user before. Are you sure you would like to proceed with this payment?"

###Feature 2
The PayMo team is concerned that these warnings could be annoying because there are many users who haven't had transactions, but are still in similar social networks. 

For example, User A has never had a transaction with User B, but both User A and User B have made transactions with User C, so User B is considered a "friend of a friend" for User A.

For this reason, User A and User B should be able to pay each other without triggering a warning notification since they're "2nd degree" friends. 

<img src="./images/friend-of-a-friend1.png" width="500">

To account for this, PayMo would like you to also implement this feature. When users make a payment, they'll be notified when the other user is outside of their "2nd-degree network".

* "unverified: This user is not a friend or a "friend of a friend". Are you sure you would like to proceed with this payment?"


###Feature 3
More generally, PayMo would like to extend this feature to larger social networks. Implement a feature to warn users only when they're outside the "4th degree friends network".

<img src="./images/fourth-degree-friends2.png" width="600">

In the above diagram, payments have transpired between User

* A and B 
* B and C 
* C and D 
* D and E 
* E and F

Under this feature, if User A were to pay User E, there would be no warning since they are "4th degree friends". 

However, if User A were to pay User F, a warning would be triggered as their transaction is outside of the "4th-degree friends network."

(Note that if User A were to pay User C instead, there would be no warning as they are "2nd-degree" friends and within the "4th degree network") 

###Other considerations and optional features

It's critical that these features don't take too long to run. For example, if it took 5 seconds when you make a payment to check whether a user is in your network, that would ruin your user experience and wouldn't be acceptable.


While PayMo is a fictional company, the dataset is quite interesting -- it's inspired by a real social network, includes the time of transaction, and the messages come from real Venmo transactions -- so feel free to implement additional features that might be useful to prevent fraudulent payments.

##Details of implementation

[Back to Table of Contents] (README.md#table-of-contents)

With this coding challenge, you should demonstrate strong understanding of computer science fundamentals. We won't be wowed by your knowledge of various available software libraries but will be impressed by your ability to pick and use the best data structures and algorithms for the job. 

We're looking for clean, well-thought-out code that correctly implements the desired features in an optimized way.

We also want to see how you use your programming skills to solve business problems. At a minimum, you should implement the three required features but feel free to expand upon this challenge or add other features you think would prevent fraud and further business goals. Be sure to document these add-ons so we know to look for them. 

###Input

Ideally, payment data would come from a real-time, streaming API, but we don't want this challenge to focus on the relatively uninteresting "DevOps" of connecting to an API.

As a result, you may assume that collecting the payments has been done for you and the data resides in two comma-delimited files in the `paymo_input` directory. 

The first file, `batch_payment.txt`, contains past data that can be used to track users who have previously paid one another. These transactions should be used to build the initial state of the entire user network.

Data in the second file, `stream_payment.txt` should be used to determine whether there's a possibility of fraud and a warning should be triggered.

You should assume that each new line of `stream_payment.txt` corresponds to a new, valid PayMo payment record -- regardless of being 'unverified' -- and design your program to handle a text file with a large number of payments. 

###Output

Your code should process each line in `stream_payment.txt` and for each payment, output a line containing one of two words, `trusted` or `unverified`. 

`trusted` means the two users involved in the transaction have previously paid one another (when implementing Feature 1) or are part of the "friends network" (when implementing Feature 2 and 3).

`unverified` means the two users have not previously been involved in a transaction (when implementing Feature 1) or are outside of the "friends network" (when implementing Features 2 and 3)

The output should be written to a text file in the `paymo_output` directory. Because we are asking you to implement a minimum of three features, your program should output to at least three text files in the `paymo_output` directory. 

Each output file should be named after the applicable feature you implemented (i.e., `output1.txt`, `output2.txt` and `output3.txt`)

For example, if there were 5 lines of transactions in the `stream_payment.txt`, then the following `output1.txt` file for Feature 1 could look like this: 

	trusted
	trusted
	unverified
	unverified
	trusted


##Description of Data

[Back to Table of Contents] (README.md#table-of-contents)

The `batch_payment.txt` and `stream_payment.txt` input files are formatted the same way.

As you would expect of comma-separated-value files, the first line is the header. It contains the names of all of the fields in the payment record. In this case, the fields are 

* `time`: Timestamp for the payment 
* `id1`: ID of the user making the payment 
* `id2`: ID of the user receiving the payment 
* `amount`: Amount of the payment 
* `message`: Any message the payer wants to associate with the transaction

Following the header, you can assume each new line contains a single new PayMo payment record with each field delimited by a comma. In some cases, the field can contain Unicode as PayMo users are fond of placing emojis in their messages. For simplicity's sake, you can choose to ignore those emojis.

For example, the first 10 lines (including the header) of `batch_payment.txt` or `stream_payment.txt` could look like: 

	time, id1, id2, amount, message
	2016-11-02 09:49:29, 52575, 1120, 25.32, Spam
	2016-11-02 09:49:29, 47424, 5995, 19.45, Food for 🌽 😎
	2016-11-02 09:49:29, 76352, 64866, 14.99, Clothing
	2016-11-02 09:49:29, 20449, 1552, 13.48, LoveWins
	2016-11-02 09:49:29, 28505, 45177, 19.01, 🌞🍻🌲🏔🍆
	2016-11-02 09:49:29, 56157, 16725, 4.85, 5
	2016-11-02 09:49:29, 25036, 24692, 20.42, Electric
	2016-11-02 09:49:29, 70230, 59830, 19.33, Kale Salad
	2016-11-02 09:49:29, 63967, 3197, 38.09, Diner
	 

##Writing clean, scalable and well-tested code
[Back to Table of Contents] (README.md#table-of-contents)

As a data engineer, it’s important you write clean, well-documented code that scales for large amounts of data. For this reason, it’s important to ensure your solution works well for a huge number of payments coming in a short period of time.

It's also important to use software engineering best practices like **unit tests**, especially since public data is not clean and predictable. For more details about the implementation, please refer to the FAQ below or email us at <mailto:cc@insightdataengineering.com>

You may write your solution in any mainstream programming language, such as C, C++, Java, JavaScript, Python, Ruby, or Scala. Once completed, submit a link to a Github (or Bitbucket) repo with your source code. 

In addition to the source code, the top-most directory of your repo must include the `paymo_input` and `paymo_output` directories, and a shell script named `run.sh` that compiles and runs the program(s) that implement these features. 

If your solution requires additional libraries, environments, or dependencies, you must specify these in your README documentation (otherwise we won't be able to test it). See the figure below for the required structure of the top-most directory in your repo, or simply <i>clone</i> this repo, but **please don't fork** it.

##Repo directory structure
[Back to Table of Contents] (README.md#table-of-contents)

Example Repo Structure

	├── README.md 
	├── run.sh
	├── src
	│  	└── antifraud.java
	├── paymo_input
	│   └── batch_payment.txt
	|   └── stream_payment.txt
	├── paymo_output
	│   └── output1.txt
	|   └── output2.txt
	|   └── output3.txt
	└── insight_testsuite
	 	   ├── run_tests.sh
		   └── tests
	        	└── test-1-paymo-trans
        		│   ├── paymo_input
        		│   │   └── batch_payment.txt
        		│   │   └── stream_payment.txt
        		│   └── paymo_output
        		│       └── output1.txt
        		│       └── output2.txt
        		│       └── output3.txt
        		└── your-own-test
            		 ├── paymo_input
        		     │   └── batch_payment.txt
        		     │   └── stream_payment.txt
        		     └── paymo_output
        		         └── output1.txt
        		         └── output2.txt
        		         └── output3.txt

The contents of `src` do not have to contain the single file called `"antifraud.java"`, you are free to include one or more files and name them as required by your implementation.

##Testing your directory structure and output format
[Back to Table of Contents] (README.md#table-of-contents)

To make sure that your code has the correct directory structure and the format of the output data in `output1.txt`, `output2.txt` and `output3.txt` is correct, we included a test script, called `run_tests.sh` in the `insight_testsuite` folder.

The tests are stored simply as text files under the `insight_testsuite/tests` folder. Each test should have a separate folder and each should contain a `paymo_input` folder -- where `batch_payment.txt` and `stream_payment.txt` files can be found. There also should be a `paymo_output` folder where `output1.txt`, `output2.txt` and `output3.txt` should reside.

From the `insight_testsuite` folder, you can run the test with the following command:

	insight_testsuite$ ./run_tests.sh 

The output of `run_tests.sh` should look like:

    [PASS]: test-2-paymo-trans (output1.txt)
    [FAIL]: test-2-paymo-trans (output2.txt)
    1c1
    < trusted
    ---
    > unverified
    [PASS]: test-2-paymo-trans (output3.txt
    [Fri Nov  4 13:20:25 PDT 2016] 2 of 3 tests passed

on failed tests and	
	
	[PASS]: test-1-paymo-trans (output1.txt)
	[PASS]: test-1-paymo-trans (output2.txt)
	[PASS]: test-1-paymo-trans (output3.txt)
	[Fri Nov  4 13:20:25 PDT 2016] 3 of 3 tests passed
on success.

One test has been provided as a way to check your formatting and simulate how we will be running tests when you submit your solution. We urge you to write your own additional tests here as well as for your own programming language. `run_tests.sh` should alert you if the directory structure is incorrect.

Your submission must pass at least the provided test in order to pass the coding challenge.

#FAQ

Here are some common questions we've received.  If you have additional questions, please email us at cc@insightdataengineering.com and we'll answer your questions as quickly as we can, and update this FAQ.

* *Which Github link should I submit?*  
You should submit the URL for the top-level root of your repository.  For example, this repo would be submitted by copying the URL `https://github.com/InsightDataScience/digital-wallet` into the appropriate field on the application.  Do NOT try to submit your coding challenge using a pull request, which would make your source code publicly available.  

* *Do I need a private Github repo?*  
No, you may use a public repo, there is no need to purchase a private repo.  You may also submit a link to a Bitbucket repo if you prefer.

* *If User A sends a payment to User B, is that different than if User B sends a payment to User A?*  
No, for simplicity all relationships should be undirected. Users are "friends" regardless of who initiated the payment.  

* *May I use R, Matlab, or other analytics programming languages to solve the challenge?*  
It's important that your implementation scales to handle large amounts of data. While many of our Fellows have experience with R and Matlab, applicants have found that these languages are unable to process data in a scalable fashion, so you should consider another language.  

* *May I use distributed technologies like Hadoop or Spark?*  
While you're welcome to do so, your code will be tested on a single machine so there may not be a significant benefit to using these technologies prior to the program. With that said, learning about distributed systems is a valuable skill for all data engineers.

* *What sort of system should I use to run my program on (Windows, Linux, Mac)?*  
You may write your solution on any system, but your source code should be portable and work on all systems. Additionally, your `run.sh` must be able to run on either Unix or Linux, as that's the system that will be used for testing. Linux machines are the industry standard for most data engineering teams, so it is helpful to be familiar with this. If you're currently using Windows, we recommend using tools like Cygwin or Docker, or a free online IDE such as [Cloud9](http://c9.io).  

* *How to handle invalid lines?*  
Firstly it should be noted that the input files are formatted as per Unix/Linux standards where newlines are dictated by a newline character ('\n'). If you are running your code on Windows or specifically detecting for carriage return ('\r') make sure that you update your code to use the correct line delimiter otherwise you may be virtually creating invalid lines. If you are confident this is not the cause of your invalid lines, please let us know at cc@insightdataengineering.com.
  
* *Can I use pre-built packages, modules, or libraries?*   
This coding challenge can be completed without any "exotic" packages. While you may use publicly available packages, modules, or libraries, you must document any dependencies in your accompanying `README` file. When we review your submission, we will download these libraries and attempt to run your program. If you do use a package, you should always ensure that the module you're using works efficiently for the specific use-case in the challenge, since many libraries are not designed for large amounts of data.

* *Can I use a database engine?*   
This coding challenge can be completed without the use of a database. However, if you must use one, it must be a publicly available one that can be easily installed with minimal configuration.

* *Will you email me if my code doesn't run?*   
Unfortunately, we receive hundreds of submissions in a very short time and are unable to email individuals if code doesn't compile or run. This is why it's so important to document any dependencies you have, as described in the previous question. We will do everything we can to properly test your code, but this requires good documentation. More so, we have provided a test suite so you can confirm that your directory structure and format are correct.

* *Do I need to use multi-threading?*   
No, your solution doesn't necessarily need to include multi-threading - there are many solutions that don't require multiple threads/cores or any distributed systems, but instead use efficient data structures.  

* *Do I need to account for an updating `stream_payment.txt` file?*   
No, your solution doesn't have to re-process `stream_payment.txt` multiple times. If you were doing this project as a data engineer in industry, you would probably connect to a RESTful API to get one transaction at a time, but this is beyond the scope of this challenge. Instead, you should imagine that each line corresponds to a new sequential transaction. 

* *What should the format of the output be?*  
In order to be tested correctly, you must use the format described above. You can ensure that you have the correct format by using the testing suite we've included. If you are still unable to get the correct format from the debugging messages in the suite, please email us at cc@insightdataengineering.com.

* *Should I check if the files in the input directory are text files or non-text files(binary)?*  
No, for simplicity you may assume that all of the files in the input directory are text files, with the format as described above.

* *Can I use an IDE like Eclipse or IntelliJ to write my program?*  
Yes, you can use what ever tools you want -  as long as your `run.sh` script correctly runs the relevant target files and creates the `output1.txt`, `output2.txt`, `output3.txt` files in the `paymo_output` directory.

* *What should be in the `paymo_input` directory?*  
You can put any text file you want in the directory since our testing suite will replace it. Indeed, using your own input files would be quite useful for testing.

* *How will the coding challenge be evaluated?*  
Generally, we will evaluate your coding challenge with a testing suite that provides a variety of inputs and checks the corresponding output.  This suite will attempt to use your `run.sh` and is fairly tolerant to different runtime environments.  Of course, there are many aspects (e.g. clean code, documentation) that cannot be tested by our suite, so each submission will also be reviewed manually by a data engineer. 

* *How long will it take for me to hear back from you about my submission?*  
We receive hundreds of submissions and try to evaluate them all in a timely manner.  We try to get back to all applicants **within two or three weeks of submission**, but if you have a specific deadline that requires expedited review, you may email us at cc@insightdataengineering.com.  


