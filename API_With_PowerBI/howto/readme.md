# No more downloading zips and excel
## Utilizing APIs to learn data analysis

1. Get data -> Others -> Web -> Click Connect
<br/>

![alt text](image.png)

3. If you are using an open API, enter URL in the basic

2.  In my case, I am using rapid API and will be using anime_db API
<br/>

![alt text](image-1.png)

3. On the left (rapidAPI), click on genres *thats the easiest to start with*
<br/>

![alt text](image-3.png)

4. On your right (rapidAPI), you will see a url https://anime-db.p.rapidapi.com/genre

<br/>

![alt text](image-2.png)

5. Copy the url and past in the powerBI

6. Similarly, you will see a host and key in the code snippets on rapidAPI, copy them one by one and paste in powerBI, make sure nothing is in QUOTATIONS

<br/>

![alt text](image4.png)

7. When you click ok, you will be prompted to a window, select anonymous. 


<br/>

![alt text](image-4.png)

- You can type a new query to add more data. 

:heavy_exclamation_mark::heavy_exclamation_mark::heavy_exclamation_mark:	Getting an authorization error? Make sure, you are subscribed to rapidAPI, if you aren't, you can now by clicking "Test the endpoint" or "Subscribe". If you get 200 results, you are good to go and everything should work on trying again. 

![alt text](image-6.png)

## A quicker way?

When you are extracting anime, you can only get 20 rows at a time (*due to pagination*), which means you have too run and establish connections multiple time. 

Alternatively, do this, 

1. In the query editor, start a new query
2. Go to advanced editore and write the following code


:heavy_exclamation_mark::heavy_exclamation_mark: Change the key to yours. 


```
let
    BaseUrl = "https://anime-db.p.rapidapi.com/anime?",
    ApiKey = "YOUR_API_KEY",  // Replace with your actual key
    Headers = [
        #"x-rapidapi-host" = "anime-db.p.rapidapi.com",
        #"x-rapidapi-key" = ApiKey
    ],
    
    // Function to fetch a single page
    GetPage = (PageNumber as number) =>
        let
            Url = BaseUrl & "page=" & Number.ToText(PageNumber) & "&size=10",
            Response = Web.Contents(Url, [Headers=Headers]),
            JsonResponse = Json.Document(Response),
            Data = JsonResponse[data]  // Adjust based on API response structure
        in
            Data,

    // Create a list of pages to fetch (e.g., 1 to 5)
    PageNumbers = List.Generate(() => 1, each _ <= 5, each _ + 1),  // Fetching first 5 pages (adjust as needed)
    
    // Get all data
    AllData = List.Transform(PageNumbers, each GetPage(_)),

    // Flatten nested lists into a single table
    CombinedData = List.Combine(AllData),
    TableData = Table.FromRecords(CombinedData)
    in
    TableData
```

As a result, you will get the anime data. 


If this helps, please star :star: and follow :+1: .