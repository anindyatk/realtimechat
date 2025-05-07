#Screening Questions for Web Developers

*Basic Questions
Imagine you're building a website that allows users to submit photos. One of the requirements is that each photo must be reviewed by a moderator before it can be published. How would you design the logic for this process? What technologies would you use? Do you have any data structure in mind to support this based on your technology of choice to handle those data?

Flow
REQUESTER
1. (FE Web) User Upload : do some validation (file type, size limits) 
2. (FE Web->BE) Submit the photos : validate, record file property in the database with the status 'pending review', copy file to storage server in the temporary directory

MODERATOR
3. (FE Web) Queries the database for photos with the status 'pending review'. Choose Approve / Reject
4. (FE Web->BE) If moderator approve the photo, change status with 'APPROVED' and move the file from temporary directory to permanent location directory.
        If moderator reject the photo, change status with 'REJECTED' and  file in the temporary directory can be deleted.

REQUESTER
5. (FE) When a user views the website, the application queries the database for photos with the status "Approved".

note : add notification (email, notification on the web)

Technology 
- FE Web : Vue.js, typescript
- BE : C#, ASP.NET WEBAPI
- File Storage : Azure Storage/Amazon S3
- Database : SQL Server (RDMS)
- Notification : WebSocket, SMPTP


* Database Quetion
1. SELECT * FROM Customers WHERE Country='Germany';
2. SELECT Country, COUNT(CustomerId) FROM Customers 
    GROUP BY Country
    HAVING COUNT(CustomerId) >= 5
    ORDER BY COUNT(CustomerId) DESC
3. SELECT Customers.CustomerName, COUNT(Orders.OrderID) AS OrderCount, 
    MIN(CONVERT(VARCHAR, Orders.OrderDate, 23)) AS FirstOrder, 
    MAX(CONVERT(VARCHAR, Orders.OrderDate, 23)) AS LastOrder 
        FROM Orders
            INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID
            GROUP BY Customers.CustomerName, Customers.Country 
            HAVING COUNT(Orders.OrderID) >=5
            ORDER BY MAX(Orders.OrderDate) DESC


*JavaScript/TypeScript Questions
- create titleCase function
function titleCase(str) {
  str = str.toLowerCase().split(' ');
  for (let i = 0; i < str.length; i++) {
    str[i] = str[i].charAt(0).toUpperCase() + str[i].slice(1);
  }
  return str.join(' ');
}

- create countWord function
function countWord(str) {
	const cleanedStr = str.toLowerCase().replace(/[^\w\s]/g, '');
	const words = cleanedStr.split(/\s+/).filter(word => word !== '');
	return words.reduce((frequencyMap, word) => {
		frequencyMap[word] = (frequencyMap[word] || 0) + 1;
		return frequencyMap;
	}, {});
}


2. Fixing Code 
  function delay(ms) {
  	return new Promise(resolve => setTimeout(resolve, ms));
  }
  delay(3000).then(() => alert('runs after 3 seconds'));
  async function fetchDataAsync(url) {
  	return new Promise((resolve, reject) => {
  		setTimeout(() => {
  			if (!url) {
  				reject("URL is required");
  			} else {
  				resolve(`Data from ${url}`);
  			}
  		}, 1000);
  	});
  }
  async function processDataAsync(data) {
  	return new Promise((resolve, reject) => {
  		setTimeout(() => {
  			if (!data) {
  				reject("Data is required");
  			} else {
  				resolve(data.toUpperCase());
  			}
  		}, 1000);
  	});
  }
  async function fetchDataAndProcess() {
  	try {
  		const fetchedData = await fetchDataAsync("https://example.com");
  		const processedData = await processDataAsync(fetchedData);
  		console.log("Processed Data:", processedData);
  	} catch (error) {
  		console.error("Error:", error);
  	}
  }
  fetchDataAndProcess();



* Create a real-time chat between two windows; using web sockets, vuejs and typescript
  (Attached)
  ChatApp
  ---- client
  --------src
  -------------components
  -----------------ChatRoom.vue
  ---- server
  --------src
  -----------server.js

To run project
go to the server folder first and then execute npm run start 
then go to client folder than execute npm run serve


* Vue.js
●	Explain Vue.js reactivity and common issues when tracking changes. 
    While Vue's reactivity is generally seamless, there are common scenarios where changes might not be automatically tracked as expected. These often involve how JavaScript handles object and array mutations.
    Adding or Removing Object Properties Dynamically (Vue 2)
 
●	Describe data flow between components in a Vue.js app 
    Unidirectional Data Flow : This makes it easier to understand how data changes and where those changes originate.
    Props Down, Events Up: This pattern enforces a clear hierarchy and makes debugging simpler. 

●	List the most common cause of memory leaks in Vue.js apps and how they can be solved.
    Unremoved Event Listeners, Unsubscribed Observables/Subscriptions, Closures Holding onto Component Instances or Large Data

●	What have you used for state management
    Vuex, Redux

●	What’s the difference between pre-rendering and server side rendering?
Pre-rendering is about generating static HTML at build time for specific routes.   
Server-side rendering is about generating dynamic HTML on the server for each incoming request.

* Website Security Best Practises
1. Keep udpated
2. Strong Passwords and Multi-Factor Authentication (MFA)
3. using HTTPS and SSL/TLS Certificates
4. Input Validation and Sanitization
5. Web Application Firewall 
6. Rate Limiting
7. Secure Error Handling

* Website Performance Best Practises
1. Caching
2. optimize asset
3. CDN

* Golang (if interviewing for a Golang job) / .NET Candidat --> choose .NET

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text.RegularExpressions;

public class WordFrequencyCounterEfficient
{
    public static Dictionary<string, int> CountWordFrequencyEfficient(string text)
    {
        if (string.IsNullOrEmpty(text))
        {
            return new Dictionary<string, int>();
        }

        // 1. Convert to lowercase and extract words using Regex
        MatchCollection matches = Regex.Matches(text.ToLower(), @"\b\w+\b");

        // 2. Create a dictionary to store word frequencies
        Dictionary<string, int> wordFrequencies = new Dictionary<string, int>();

        // 3. Iterate through the matches and count word occurrences
        foreach (Match match in matches)
        {
            string word = match.Value;
            if (wordFrequencies.ContainsKey(word))
            {
                wordFrequencies[word]++;
            }
            else
            {
                wordFrequencies.Add(word, 1);
            }
        }

        return wordFrequencies;
    }

    public static void Main(string[] args)
    {
        string text = "Four, One two two three Three three four four four";
        Dictionary<string, int> frequency = CountWordFrequencyEfficient(text);

        foreach (KeyValuePair<string, int> pair in frequency.OrderBy(kvp => kvp.Key))
        {
            Console.WriteLine($"{pair.Key} => {pair.Value}");
        }
    }
}


Tools (Rate yourself 1 to 5)
●	Git : 5
●	Redis : 5
●	VSCode / JetBrains?  VSCode : 5, JetBrains : 4
●	Linux? : 5
●	AWS
○	EC2 : 3
○	Lambda : 3
○	RDS : 3
○	Cloudwatch : 3
○	S3 : 3
●	Unit testing : 5
