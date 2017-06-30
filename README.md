# The Blue Alliance API Java Library [![Build Status](https://travis-ci.org/spencerng/TBA-API-Java-Library.svg?branch=master)](https://travis-ci.org/spencerng/TBA-API-Java-Library)

Java client library to retrieve data from The Blue Alliance using TBA API v3

Full Javadoc documentation can be found [here](http://spencerng.github.io/TBA-API-Java-Library/)

## Usage

Begin by creating a TBA object with your Read TBA API Key. This can be found or generated on your [account dashboard](https://www.thebluealliance.com/account).

    String authKey = // your TBA API read key
    TBA tba = new TBA(authKey);

### Regular Usage

The library allows access to almost all of the calls in [The Blue Alliance API v3 documentation](https://www.thebluealliance.com/apidocs/v3). 

They are grouped into requests with team, event, district, or match parameters, and you will need to use the `teamRequest`, `eventRequest`, or `matchRequest` instance variables found in the [`TBA` class](http://spencerng.github.io/TBA-API-Java-Library/com/thebluealliance/api/v3/TBA.html).

Here is an example of retrieving an array of teams in the FIRST Mid-Atlantic district in 2017:

    Team[] midAtlanticTeams = tba.districtRequest.getTeams("2017mar");

A list of request methods for each request object can be found [here](http://spencerng.github.io/TBA-API-Java-Library/com/thebluealliance/api/v3/requests/package-summary.html).

### Advance Usage

If you want to utilize the `If-Modified-Since` and `Last-Modified` headers, you will need to make a direct URL request with the [`getDataTBA(String urlDirectory)` method](http://spencerng.github.io/TBA-API-Java-Library/com/thebluealliance/api/v3/requests/DataRequest.html#getDataTBA-java.lang.String-java.lang.String-) in the [`DataRequest` class](http://spencerng.github.io/TBA-API-Java-Library/com/thebluealliance/api/v3/requests/DataRequest.html). This will return an [`APIResponse`](http://spencerng.github.io/TBA-API-Java-Library/com/thebluealliance/api/v3/requests/APIResponse.html) object with JSON data, the HTTP response code, and the `Last-Modified` header. 

The JSON data will need to be deserialized into an object model with a method in the [`Deserializer` class](http://spencerng.github.io/TBA-API-Java-Library/com/thebluealliance/api/v3/Deserializer.html) before being used.

Here is an example of continuously fetching the `Match` objects for the 2017 Mount Olive District Event, if they have been updated. Note that this is very inefficient.

	APIResponse resp = tba.dataRequest.getDataTBA("/event/2017njfla/matches");
	String lastModified = resp.getLastModified();
	Match[] matchList = Deserializer.toMatchArray(resp.getJson());

	while(true){
		resp = tba.dataRequest.getDataTBA("/event/2017njfla/matches", lastModified);

		if(resp.getResponseCode()!=304){ // HTTP code 304 indicates no change
			teamList = Deserializer.jsonToTeamArray(resp.getJson());
			lastModified = resp.getLastModified();
		}
	}

## Models

A list of object model classes and their getter methods for instance variables can be found [here](http://spencerng.github.io/TBA-API-Java-Library/com/thebluealliance/api/v3/models/package-summary.html)

## Contact

Feel free to contact me at sng1488@gmail.com or create a pull request if you have any questions, fixes, or suggestions.