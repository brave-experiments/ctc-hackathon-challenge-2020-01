# Feature List
This is a proof-of-concept.
There should be basic functionality, it shouldn't crash too often, and it should look nice;
However,
a number of features required for an FCS are (purposefully) omitted.

Instead of spending any time on login screens, setting pages, help or feedback links, 
let's just get the basics in place and make it look good!

Take a look at the [Landmarks project](https://developer.apple.com/tutorials/swiftui/creating-and-combining-views) an approach.
We don't have to start with this project,
but it may be faster/easier to simply morph it into what we want.

# User Experience
Launching the app gets the geolocation permission for when the app is running.

On first-time initialization, show the current location; otherwise, show whereever the map was before.

Use Apple Maps: 

- Include the usual touch points (center the map on the current location, show map info)

- Include a search bar

- Include a touch point to add an entry

- Show existing entries with an icon:

    - Make a network call to get the coordinates of all entries visible for the map
    
    - Data in the results indicates whether an entry was submitted by the user or someone else (different icons)

    - Tapping on the icon brings up the information on the entry

- Periodically reload the data to update the map

## Add Entry
For POC, only "Green Iguana" will be reported.

User input:

- Get a photo from the camera (stretch goal: from photo library)

- Allow optional textual annotation

After confirmation,
makes a network call to upload the data.

No preference as to the modality of the dialog, whether a new screen is required, etc.

# Network Calls
All interactions use HTTPS.
All HTML bodies are JSON.
Precise details are TBD.

(There is some thinking on what is needed for a FCS;
accordingly, some of the fields below are presently defaults for future use.)

## Submit Entry
`POST` request  with:

- owner UUID (generated for each post)
- category (i.e., `'green-iguana'`)
- position: longitude, latitude, (optional) elevation 
- photo
- (optional) description

Response with:

- Public UUID

## Get Entries
`GET` request with geofence parameters:

- shape: `'circle'`
    - center position
    - radius in meters

The server responds with an array of matching entries.
each containing:

- the public UUID assigned by the server on creation

- all information that was submitted,
EXCEPT that the owner UUID is omitted
