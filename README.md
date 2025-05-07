This is the first page the user sees when opening the web application, this page allows the user to upload their own encrypted DJI drone flight log to analyze. There is a description of how to import files for decryption which is offered to the user. The firstirst step of the description is to name the log they want to upload by typing in the input field above the upload button. This is the name that the log will be given in the database. 

Secondly, the description advises the user to select a file from their local computer. If the user selects an unsupported file, they will get an error message on the page. On the other side with a supported file the client sends a POST request with the filename user input and the uploaded file to the server /api/convert?filename=[name] endpoint. Here, the server(ParserController.ts) handles the decrypted data and converts them to a GeoJson-formatted string. 

This GeoJson string is then hashed using the SHA-256 hashing algorithm within Deno's standard crypto library. When the the unique hash is made, the controller checks whether the same log has already been uploaded to the database via the supabase client. if it is then we get an error saying "duplicate found". The user can then press "view existing log" to analyze the log they uploaded.

If there is not duplicate found in the database the ParserContoller.ts sends the first valid coordinates in the GeoJson string to the LocationController.ts. The LocationController.ts uses these coordinates to reverse geocode using locationIQ API. If the GET response from locationIQ is ok we get the location data from those coordinates. 

Finally we send the POST request to the supabase client, which will upload the the uploaded file with all of this data into the correct fields in the database.
