# TODO

# Assets
- App icon for iOS
- Marker icons:
    - iOS: public and private
    - Operator map: approved or not
    
# Server-side    
## Workflow
- Remove temporary metadata for `approved/authority`

- Data reducation

- Data aging

- Statistical analysis
    
## Provisioning
- Determine how to obscure IP addresses to the server
    
## API
- Add a `/v1/region/{regionID}/feed` call for [RSS](https://en.wikipedia.org/wiki/RSS) clients.
    
# iOS application
## Correctness
- `MainViewController::updateMapPins` should calculate the distance from the center of the map to a corner and use that value as the radius parameter to `IguanaClient.default.getEntries`.
(At present, it appears to ask for entries with 50m of the current location.)
    
- A call to `IguanaClient.default.getEntries` should be made after 60 seconds of inactivity as the active app.
    
## Usability
- Ensure that the `My Location` icon has a very small touch point (in most cases, folks are going to see an icon right next to their location and if they tap there, you want the entry to open up, not the `my location`.

- The `Locate Me` icon in the lower right-hand corner sits on top of the "Legal" link --
 it should be moved somewhere else (i'm thinking the upper right-hand corner).

## Completeness
- Get an application icon.
    
- The entry detail popup should contain:
    
    - reverse geocoding information; and,
    
    - the creation date/time -- `entry.metadata.created` -- cf.,
    [milliseconds since the epoch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date).

- From an entry's detail popup, allow the user to:

    - modify the description; and,

    - delete the entry (upon confirmation).

- `IguanaClient.default.getEntries` should include the category (e.g., "green-iguana").    

- There should be a "Search" bar (similar to what is in the Apple Maps app), e.g., 
      magnifying glass, cancel button, text display that works in dark mode
    
- There should be a "Settings" icon that transitions to a panel that contains:
    
    - any "actual" settings;
    - Privacy Policy (obscured IP addresses, privateID); and,
    - Terms and Conditions
    
- There should be a pre-build script that let's the developer select the
  category (e.g., "green-iguana"), enter the API key,

# Android application
Find someone to write one with feature-parity to the iOS application.

# Operator map
- The entry detail popup should contain:

    - a link to approve/delete the entry.
    
- Add a [sidebar](https://github.com/nickpeihl/leaflet-sidebar-v2/):

    - select which region(s) to poll;
    - add a ["Locate Me" button](https://github.com/domoritz/leaflet-locatecontrol); and,
    - add an [auto-completion bar](https://github.com/utahemre/Leaflet.GeoJSONAutocomplete).
