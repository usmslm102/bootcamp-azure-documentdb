# Getting Started with Azure DocumentDB

**Azure DocumentDB** is a fully-managed, highly-scalable, NoSQL document database service provided by Azure. Its many benefits include rich query over a schema-free JSON data model, transactional execution of JavaScript logic, and scalable storage and throughput. You can find out more about DocumentDB online in the [Microsoft Azure subsite for DocumentDB](http://azure.microsoft.com/en-us/services/documentdb/).

In this lab, you will learn how to:

  * [Create a DocumentDB database account](#creating-a-documentdb-database-account)
  * [Import data using the DocumentDB data migration tool](#import-data-to-documentdb)
  * [Run queries using the DocumentDB Query Explorer](#run-queries-using-query-explorer)
  * [Connect to a real world application](#connect-to-a-real-world-application)

<a name="creating-a-documentdb-database-account"></a>
## Create a DocumentDB database account

1.	Sign in to the [Microsoft Azure portal](https://portal.azure.com/).
2.	In the Jumpbar, click **New +**, then select **Databases**, and then click **DocumentDB**. 

![Screen shot 001][1]
   
3. In the  **New NoSQL (DocumentDB) account** blade, specify the desired configuration for the new account. 
 
	![Screen shot 002][2] 
- In the **Id** box, enter a name to identify the DocumentDB account. This value becomes the host name within the URI.
- Select *DocumentDB* under **NoSQL API**
- For **Subscription**, select the Azure subscription that you want to use for the DocumentDB account. If your account has only one subscription, that account will be selected automatically.
- In **Resource group**, select or create a resource group for your DocumentDB account.  By default, a new Resource group will be created.  You may, however, choose to select an existing resource group to which you would like to add your DocumentDB account. For more information, see [Using resource groups to manage your Azure resources](resource-group-portal.md).
- Use **Location** to specify the geographic location in which your DocumentDB account will be hosted.   

4.	Once the new DocumentDB account options are configured, click **Create**.  It will take ~10 minutes for the DocumentDB account to be created.  To check the status, you can monitor the progress via the Notification hub in the top right.  
	![Screen shot 003][3]

	![Screen shot 004][4]
  
5.	While the account is provisioning, familiarize yourself with DocumentDB's SQL query grammar by trying out some sample queries on the [DocumentDB Query Playground](http://www.documentdb.com/sql/demo). 

	![Screen shot 005][5]

6.	After the DocumentDB account has been created you can find it under 'All Resources'.

	![Screen shot 006][6]


<a name="import-data-to-documentdb"></a>
## Import data to DocumentDB
### Overview of the DocumentDB Data Migration Tool
The DocumentDB Data Migration tool is an open source solution that imports data to DocumentDB from a variety of sources, including:
- JSON files
- MongoDB
- SQL Server
- CSV files
- Azure Table storage
- DocumentDB collections

While the import tool includes a graphical user interface (dtui.exe), it can also be driven from the command line (dt.exe).  In fact, there is an option to output the associated command after setting up an import through the UI.  Tabular source data (e.g. SQL Server or CSV files) can be transformed such that hierarchical relationships (subdocuments) can be created during import.

The migration tool is open source and can be found on GitHub in [this repository](https://github.com/azure/azure-documentdb-datamigrationtool) 
<a name="import-json"></a>
### Import JSON files
The JSON file source importer option allows you to import one or more single document JSON files or JSON files that each contain an array of JSON documents.  When adding folders that contain JSON files to import, you have the option of recursively searching for files in subfolders.
1.  Download the JSON sample data: [Products.json](media/Products.json)
2.	Download the migration tool from the [Microsoft Download Center](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d).
3.	Extract the migration tool to a directory of your choice.
4.	Run **Dtui.exe** to open the graphical interface version of the tool
5.	Once the **Welcome** screen displays, click **next** to proceed.
6.	On the **Source Information** screen, select the **JSON file(s)** option from the **Import from** drop-down menu.
7.	Click **Add Files** and select to the sample JSON data set you downloaded in step 1. Click **Next** to proceed.
 ![Screen shot 007 Import JSON][7]
8.	On the **Target Information** screen, you will see a number of export target options. Select the **DocumentDB - Bulk import** option. *This allows you to import to a DocumentDB using a stored procedure for efficiency.  The tool will create, execute, and then delete the stored procedure from the target collection.**
![Screen shot 008 Import JSON][8]
9.	Switch to the **Azure Portal** and navigate to the **Keys menu** for your DocumentDB account. 
10. Copy the **Primary Connection String** (this also contains the *Primary Key*)

	![Screen shot 009 Import JSON][9]

11. Now switch back to the importer. Before pasting the connection string into the Import tool you need to append a **Database** name. You can fill in an existing name or make one up and let the Import tool create a new database for you.
	
	```xml
	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;
	```

12. Click **Verify** to ensure that the DocumentDB instance specified in the connection string field can be accessed. 

	![Screen shot 010 Import JSON - Database name and Verify][10]

13. Next, enter the name of the *Collection* to which the data will be imported. For example: *Products*
14. Click **Next** to proceed to the 'Advanced' page. Click **Next** again to proceed to the *Summary*.
    > You can leave the *Partition Key* and *Id Field* blank. The default value of 1000 for *Collection Throughput* can be left as is.


15. The **Summary** page allows you to review your source and target options.

	![Screenshot 011 - Summary][11]

16. Once you are satisfied, click **Import**. Importing takes a rough 5 seconds. When completed you can close the *DocumentDB Data Migration Tool*.
17. In the Azure Portal you can now find your database under *Collections/Browse*
		
	![Screenshot 012 - Collections/Browse][12]


<a name="run-queries-using-query-explorer"></a>
##  Run queries using the DocumentDB Query Explorer
The DocumentDB **Query Explorer** enables you to create, edit, and run queries against a DocumentDB collection. The Query Explorer can be launched from the Azure Portal from any of the DocumentDB account, database, and collection blades.
  
1. Near the bottom of each DocumentDB blade you can find the **Query Explorer** under the *Collections* group.
	
	![Screenshot 013 - Query Explorer][13] 

2. Select the Database and Collection you created in the previous section.
    > If you only have a single database and collection they will be selected for you.

3. Try running following queries, and examine the query results:
	
	1.	Grab all the product documents 
		```SQL 
		SELECT * FROM Products
		```

    2.  Select a specific product by ProductNumber 
		```SQL 
		SELECT *
		FROM Products p
		WHERE p.ProductNumber = "BK-M38S-42"
		```
    3. Select specific properties
	
		> Note: the referencing hierarchy in the SELECT clause.
		```SQL
		SELECT	
			p.id, 
			p.Name, 
			p.Description,
			p.Size, 
			p.Weight, 
			p.Category.Name AS Category
		FROM Products p
		WHERE p.ProductNumber = "BK-M38S-42"
		```

    4. Now try using more [advanced query operators](http://azure.microsoft.com/en-us/documentation/articles/documentdb-sql-query/#in-keyword), such as the `IN` operator.
		```SQL
		SELECT p.id, p.Name
		FROM Products p
		WHERE p.id IN ("680","706","707","708")
		```


4. Now that we've confirmed that the database is up and running and we've played a bit with the syntax, we can try to connect it to a 'real' application.


<a name="connect-to-a-real-world-application"></a>
##  Connect to a *real world* application


1. Open Visual Studio 2017 and create a new **.NET Core Console App** application.
![Screenshot 014 - New .NET Core Console App][14]
 
2. To be able to connect to the Azure DocumentDB we need the **Microsoft.Azure.DocumentDB.Core** NuGet package. Therefore follow the next steps
    1. Right click the project and select 'Manage NuGet Packages in the context menu.
    2. In the Nuget Explorer switch to the *Browse* tab and search for 'Azure documentdb'
    3. From the result list select the DocumentDB library with .NET Core mentioned: **Microsoft.Azure.DocumentDB.Core**
    4. Finally click *Install* followed by *Accept* on the several license agreements.
		![Screenshot 015 - Adding DocumentDB NuGet pacakge to the .NET Core Console App][15]
	
	Before diving into the DocumentDB we need to add some object classes to be able to parse our JSON result into real objects. 
3. Create a new public class called **Category** and add a public int *Id* and a public string *Name*.
	```C#
    public class Category
    {
        public int Id { get; set; }
        public string Name { get; set; }
	```
4. Append the *Newtonsoft.Json* library to the class as a *using* and override the ToString() function like below:
	```C#
	using Newtonsoft.Json;

        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
	```
5. Next create a new public class called **Product** which mimics the object we have in the JSON file.
6. Withi the Product class create the following five public properties:
  	1. Property: **Id**, Type: *String*
    2. Property: **Name**, Type: *String*
    3. Property: **Description**, Type: *String* 
    4. Property: **ProductNumber**, Type: *String*
    5. Property: **Category**, Type: *Category* (the class we created before)
7. Looking at the JSON we can see the Products Id is written with a non-capital 'i'. 
	![Screenshot 016 - JSON property naming][16]
	
	Because naming has to match exactly we need to append an attribute to the Id property in this class. To do this we again need to reference the Newtonsoft.Json library.

8. Now right above the 'Id' property write the following code: '[JsonProperty(PropertyName = "id")]' which should make the Id property look like this:
	```C#
	[JsonProperty(PropertyName = "id")]
    public string Id { get; set; }	
	``` 
9. As in the *Category* class we again need to override the ToString() method within the **Product** class with a JsonConvert.SerializeObject(this).
10. The final **Product** class should look like this
	```C#
	using Newtonsoft.Json;

	namespace AzureDocumentDBConsole
	{
		public class Product
		{

			[JsonProperty(PropertyName = "id")]
			public string Id { get; set; }
			public string Name { get; set; }
			public string Description { get; set; }
			public string ProductNumber { get; set; }
			public Category Category { get; set; }

			public override string ToString()
			{
				return JsonConvert.SerializeObject(this);
			}
		}
	}
	```


11. Now switch back to the Program.cs class.
>For this example application we create a few private properties for the sake of simplicity. Please never do this in a real world situation.

12. Within the Program class right before the Main function we create a couple of private properties containing our essential DocumentDB information.
      1. A private const *string* **DatabaseName** which you can set to the name of your database (which you can find in you Azure Portal)
      2. A private const *string* **CollectionName** containing the collectionname.
			      ![Screenshot 018 - DocumentDB essentials][18]

      3. A private const *string* **EndpointUri** directing to your DocumentDB endpoint which can be found under the 'Keys' tab in the Azure environment (as described in the [import Json](#import-json) chapter)
      4. A private const *string* **PrimaryKey** containing the PrimaryKey of the DocumentDB endpoint.
      5. A private *DocumentClient* **documentClient** which the 'Microsoft.Azure.Documents.Client' library (which is already added when we downloaded the DocumentDB Nuget package)

	The code should look something like this obviously depending on how you named your database and you collection in Azure.
	![Screenshot 017 - DocumentDB essentials][17]

Now to a bit more complicated stuff. By default the query can be executed synchronously. But we don't want to hang our application, even if it's a demo application it's better to always work with a asynchronous pattern so you interface is always responding to input.
We create a simple async handler to our query input.

13. Within the Program.cs write the following function.
	```C#
	private static async Task<IEnumerable<T>> QueryAsync<T>(IQueryable<T> query)
	{
		var documentQuery = query.AsDocumentQuery();
		var result = new List<IEnumerable<T>>();

		do
		{
			var response = await documentQuery.ExecuteNextAsync<T>();
			result.Add(response);
		}
		while (documentQuery.HasMoreResults);

		return result.SelectMany(x => x);
	}
	```
	The code above converts the incoming query to a DocumentQuery (which supports pagination and asynchronous execution)
	Within the Do/While loop the query is executed until there are no more results. Every response object is written to the result IEnumerable.
	Finally the resultset is flattened by the SelectMany query and returned.

14. So we now have our basic query handler in place, now we need to create a query. The following function takes care of that while returning an executable Task object. (which we thus can run async)
	```C#
    private Task<IEnumerable<Product>> ExecuteProductQueryAsync()
    {
        var queryUri = UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName);
        
        var query = documentClient.CreateDocumentQuery<Product>(queryUri)
            .Where(p => p.ProductNumber == "FR-R92R-58");

        return QueryAsync(query);
    }
	```
	a. In the first line we instantiate the queryUri by using the UriFactory's CreateDocumentCollectionUri. The UriFactory can be found in the 'Microsoft.Azure.Documents.Client' library which is included with the nuget DocumentDB nuget package we downloaded earlier.
	Here you set the databasename and the collection you want to query. 
    b. Next we create the final query we want to execute based on the queryUri. For this example we request a specific product with the productnumber being: 'FR-R9R-58'. We can use Linq expressions in this query build.
    c. Finally we send the query to the QueryAsync function we created in the previous step and return the Task object.

15. Next step is to connect to the DocumentDB client and to invoke the query.
    a. Create a private, async function returning a Task object and name the function Start.
    b. Set the *documentClient* by creating a new DocumentClient() object using the static EndpointUri and PrimaryKey which we defined at the top of our application.
    c. Call and await the ExecuteProductQueryAsync() function. Save the result values in a variable named 'result'.
    d. Then iterate over the result collection and for each item within the resultset create a Console.WriteLine to show the result in the Console.
    
	The Start() function should look something like this:
	```C#
        private async Task Start()
        {
            documentClient = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
            var result = await ExecuteProductQueryAsync();

            foreach (var product in result)
            {
                Console.WriteLine($"Result: ProductId: {product.Id} \t Name: {product.Name} \t ProductNumber: {product.ProductNumber} \t Category: {product.Category.Name}");
            }
        }
	```
16. Now we can wrap-up this application by calling the Start() function from within the Main(). To do so replace the default Main() function with the code below. This code calls the Start() function and awaits its result while intercepting any exceptions if anything goes wrong.
	```C#
        static void Main(string[] args)
        {
            try
            {
                var program = new Program();
                program.Start().Wait();
            }
            catch (Exception ex)
            {
                var baseException = ex.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", ex.Message, baseException.Message);
            }
            finally
            {
                Console.WriteLine("Press any key to exit.");
                Console.ReadKey();
            }
        }
	``` 

17. And there you have it. Now you should be able to run and test the console application. If all goes well you should see the following result:

	![019 - Console result][19]

	Now that we have a simple result we can create a bit more complex query. 
	Let's say for instance we want all products that have a productnumber starting with the text 'FR' and having a Category that starts with the text: 'Mountain'.
    
18. Luckily we have Linq in place so we can easily adjust the query to do so:
	```C#
	var query = documentClient.CreateDocumentQuery<Product>(queryUri)
                .Where(p => p.ProductNumber.StartsWith("FR"))
                .Where(p => p.Category.Name.StartsWith("Mountain"));
	```

19. Now run the solution.
20. You'll see that you'll get an exception stating something like *"An invalid query has been specified with filters against path(s) that are not range-indexed. Consider adding allow scan header in the request."* 
	
	The thing is that nowhere in the 'database' have we configured something like an index or primarykey. 

21. To workaround this issue you can use the *FeedOptions* and set the **EnableScanInQuery** option to **true**.
    ```C#
		var feedOptions = new FeedOptions { EnableScanInQuery = true };
		var query = documentClient.CreateDocumentQuery<Product>(queryUri, feedOptions)
			.Where(p => p.ProductNumber.StartsWith("FR"))
			.Where(p => p.Category.Name.StartsWith("Mountain"));
	```

22. Now run the code again. The end result should look familiar to this:

![020 - Final console result][20]


## Summary
By completing this lab you have learned how to get started with Azure DocumentDB. 
This includes creating a new account, importing data, running queries, and executing a query via a console application. 
For more information, please check out [documentdb.com](http://www.documentdb.com)

<!--Image references-->
[1]: media/001_New_Databases_NoSQL.png
[2]: media/002_New_Databases_NoSQL.png
[3]: media/003_New_Databases_NoSQL.png
[4]: media/004_New_Databases_NoSQL.png
[5]: media/005_New_Databases_NoSQL.png
[6]: media/006_New_Databases_NoSQL.png
[7]: media/007_Import_JSON.png
[8]: media/008_Import_JSON.png
[9]: media/009_Import_JSON.png
[10]: media/010_Import_JSON.png
[11]: media/011_Import_JSON.png
[12]: media/012_Import_JSON.png
[13]: media/013_Query.png
[14]: media/014_Console.png
[15]: media/015_Console.png
[16]: media/016_Console.png
[17]: media/017_Console.png
[18]: media/018_Console.png
[19]: media/019_Console.png
[20]: media/020_Console.png
