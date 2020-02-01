# ctc-hackathon-challenge-2020-01
Cayman Tech City Hackathon Challenge

The server code is [here](https://github.com/brave-experiments/ctc-hackathon-challenge-2020-01-server).

The iOS app is [here](https://github.com/brave-experiments/IguanaBeGonna).

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

# Network API
All interactions use HTTPS,
and must include this HTTP header:

    Authorization: Bearer ...

(where `"..."` is an access token provided by the operator and "baked into" the application).

All HTML bodies are JSON.

(There is some thinking on what is needed for a FCS;
accordingly, some of the fields below are presently defaults for future use.)

## Create an entry

Supply the `privateID` (a new UUIDv4):

    curl -X POST "http://127.0.0.1:3004/v1/entry" \
      -H "accept: application/json"               \
      -H "Content-Type: application/json"         \
      -H "Authorization: Bearer ..."              \
      -d "{ \"privateID\"   : \"b2d25937-14f1-48b1-9cf3-7cfeb17b13dd\"
          , \"category\"    : \"green-iguana\"
          , \"description\" : \"CEC HQ\"
          , \"location\"    : { \"longitude\": 19.299706, \"latitude\": -81.381807 }
          , \"image\"       : { \"data\": \"...base64 encoded text...\"  }
          }"

Mandatory payload:

- `privateID` must be a new [UUIDv4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random));

- `category` (must be `"green-iguana"`);

- `description` (may be empty);

- `location.longitude` and  `location.latitude`; and,

- `image.data` must be a [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) file encoded using
[base64](https://en.wikipedia.org/wiki/Base64)


On success,
the server stores the entry and returns the `publicID`:

    {
      "publicID": "bdc8d077-d710-434e-9d9b-ba21781199af"
    }

The application should persist the `privateID` it creates,
as some operations require this.

The application should also persist the corresponding `publicID` the server creates,
so entries may be displayed as either created by this user or another user.

## Find nearby entries

Supply the geolocation and the radius (e.g., 2000 meters):

    curl -X GET "http://127.0.0.1:3004/v1/entries/circle/2000?longitude=19.348461&latitude=-81.381988"  \
      -H "accept: application/json"                                                                     \
      -H "Authorization: Bearer ..."

Mandatory parameters:

- longitude and latitude; and,

- radius (in meters)

Optional parameters:

- `category` (must be `"green-iguana"`); and,

- `limit` on the number of returned entries (must not exceed 25)

On success,
the server returns an array of entries:

    [
      { "publicID"    : "bdc8d077-d710-434e-9d9b-ba21781199af"
      , "category"    : "green-iguana"
      , "description" : "CEC HQ"
      , "location"    :
        { "longitude" : 19.299706
        , "latitude"  : -81.381807
        },
      , "image":
         { "data"     : "...base64 encoded text..."
         , "format"   : "png"
         , "width"    : 64
         , "height"   : 64
        }
      , "metadata":
         {
         }
      }
    ]

## Retrieve an entry

Supply the `privateID`:

    curl -X GET "http://127.0.0.1:3004/v1/entry/b2d25937-14f1-48b1-9cf3-7cfeb17b13dd" \
      -H "accept: application/json"                                                   \
      -H "Authorization: Bearer ..."

On success,
the server returns that entry:

    { "publicID"    : "bdc8d077-d710-434e-9d9b-ba21781199af"
    , "category"    : "green-iguana"
    , "description" : "CEC HQ"
    , "location"    :
      { "longitude" : 19.299706
      , "latitude"  : -81.381807
      },
    , "image":
       { "data"     : "...base64 encoded text..."
       , "format"   : "png"
       , "width"    : 64
       , "height"   : 64
      }
    , "metadata":
     { 
     }
    }

## Update an entry

Supply the `privateID` and the values to update:

    curl -X PUT "http://127.0.0.1:3004/v1/entry/b2d25937-14f1-48b1-9cf3-7cfeb17b13dd" \
      -H "accept: application/json"                                                   \
      -H "Content-Type: application/json"                                             \
      -H "Authorization: Bearer ..."                                                  \
      -d "{ \"category\"    : \"green-iguana\"
          , \"description\" : \"CEC HQ\"
          , \"location\"    : { \"longitude\": 19.299706, \"latitude\": -81.381807 }
          , \"image\"       : { \"data\": \"...base64 encoded text...\"  }
          }"

The payload must contain at least one of these four properties.

## Remove an entry

Supply the `privateID`:

    curl -X DELETE "http://127.0.0.1:3004/v1/entry/b2d25937-14f1-48b1-9cf3-7cfeb17b13dd" \
      -H "accept: application/json"                                                      \
      -H "Authorization: Bearer ..."

On success,
the server returns an empty object:

    { }
