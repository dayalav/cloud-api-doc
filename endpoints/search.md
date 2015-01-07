# Search

The Eyefi Cloud allows for an extensive number of search operations.  This is through either ad-hoc searching with
GET /search/files or through the saved searches with POST /search/saved and GET /search/saved/{searchId}/files.
Please note that the underlying saved search capability is near real-time but not synchronous with updates so it 
may take a few seconds for search results to update when changes are made to searched objects or queries have just
been created or updated.  GET /search/files however, does return search results synchronously when it's called.

| Method | Description |
|--------|-------------|
| [GET /search/files](#get-searchfiles) | Gets a list of files that match the search criteria. |
| [GET /search/saved](#get-searchsaved) | Gets the list of saved searches available ot a user. |
| [POST /search/saved](#post-searchsaved) | Create a new saved search. |
| [PUT /search/saved/{searchid}](#put-searchsaved) | Update a saved search. |
| [DELETE /search/saved/{searchid}](#delete-searchsaved) | Delete a saved search. |
| [GET /search/saved/{searchId}/files](#get-searchsavedsearchIdfiles) | Gets a list of files that match the saved search criteria. |

## GET /search/files

Creates an ad-hoc search and returns the file list

### Authorization

`user`

### Parameters

| Name | Type | Description |
|------|:----:|-------------|
| Page | integer | When retrieving multiple pages of files, this is the page number to retrieve |
| Per_Page | integer | When retrieving multiple pages of files, this is the number of files per page to retrieve (default 100) |
| favorite | boolean | Indicator if the file is favorited (not yet implemented in client UI). |
| in_trash | boolean | Indicator if trash is to be searched (default false). |
| has_geodata | boolean | Indicator if geodata is required for the file to be included in the search results. |
| geo_lat | float | Geographic Latitude.  Requres geo_lon and geo_distance. |
| geo_lon | float | Geographic Longitude.  Requres geo_lat and geo_distance. |
| geo_distance | string | Geographic Distance.  Integer format followed by 'mi' or 'km' for miles or kilometers.  Requires geo_lat and geo_lon.  If Geographic coordinates are specified in the search criteria then the file will be included in the search results if it fits within the specified area. |
| album_ids | string | Files need to be in the list of album ids to be included in search results.  Formatted as a string with comma separated integers. |
| event_ids | string | Files need to be in the list of event ids to be included in search results.  Formatted as a string with comma separated integers. |
| tag_ids | string | Files need to be in the list of tag ids to be included in search results.  Formatted as a string with comma separated integers. |
| camera | string | Camera name from exif data to include in search results.  This is a keyword match as opposed to a full string match. |
| date_from | string | Start date in the format of YYYY-MM-DD.  Photos need to be taken after date_from to be included in search results. |
| date_to | string | End Date in the format of YYYY-MM-DD.  Photos need to be taken before date_to to be included in search results. |
| page | integer | Request page to allow for paged search results (default 1). |
| per_page | integer | Items per page (default 10). |

### Response

```JSON
{
  "total_count": integer,
  "items": {
             [
               {
                 "id": integer,
                 "name": "string",
                 "media": "string",
                 "bytes": integer,
                 "date_time_taken": "Date",
                 "size": {
                   "width": integer,
                   "height": integer
                 },
                 "exif": {
                   "exposure_comp": "string",
                   "shutter_speed": "string",
                   "flash": "string",
                   "metering_mode": "string",
                   "lens": "string",
                   "camera": "string",
                   "iso": "string",
                   "focal_length": "string",
                   "aperture": "string"
                 },
                 "gps": {
                   "lat": float, 
                   "lng": float
                 },
                 "thumbnails": {
                   "url": "string"
                   "s640": "string",
                   "s1280": "string",
                   "s2048": "string",
                 }
               }
             ]
           }
}
```

| Key | Type | Description |
|------|:----:|-------------|
| total_count | integer | Total number of items available regardless of Page_Size.  The 'items' set size is limited by the Page_Size |
| id | integer | File ID. |
| name | string | File name. |
| media | string | Full URL that can be used to download the file. |
| bytes | integer | Size of the file in bytes. |
| date_time_taken | Date | Date and time the file was taken. |
| size / width | integer | The file's width in pixels. |
| size / height | integer | The file's height in pixels. |
| exif | dictionary | The file's exif information as provided by the camera. |
| gps lat/lng | float | The location where the photo was taken or synchronized. |
| thumbnails / url | string | Base URL for the thumbnail; append file size to return specific sizes. |
| thumbnails / s640 | string | Returns a 640px thumbnail. |
| thumbnails / s1280 | string | Returns a 1280px thumbnail. |
| thumbnails / s2048 | string | Returns a 2048px thumbnail. |

### Status Codes

| Code | Reason |
|------|-------------|
| 200 | Request has succeeded. |

## GET /search/saved

Gets the list of saved searches available ot a user.

### Authorization

`user`

### Response

```JSON
[
  {
    "id": integer,
    "name": string,
    "query": {
      "favorite": string,
      "in_trash": string,
      "has_geodata": string,
      "album_ids": string,
      "event_ids": string,
      "tag_ids": string,
      "camera": string,
      "date_from": string,
      "date_to": string,
      "geo": {
        "lat": float, 
        "lng": float,
        "distance": string
      }
    } 
  }
]
```

| Key | Type | Description |
|------|:----:|-------------|
| favorite | boolean | Indicator if the file is favorited (not yet implemented in client UI). |
| in_trash | boolean | Indicator if trash is to be searched (default false). |
| has_geodata | boolean | Indicator if geodata is required for the file to be included in the search results. |
| geo_lat | float | Geographic Latitude.  Requres geo_lon and geo_distance. |
| geo_lon | float | Geographic Longitude.  Requres geo_lat and geo_distance. |
| geo_distance | string | Geographic Distance.  Integer format followed by 'mi' or 'km' for miles or kilometers.  Requires geo_lat and geo_lon.  If Geographic coordinates are specified in the search criteria then the file will be included in the search results if it fits within the specified area. |
| album_ids | string | Files need to be in the list of album ids to be included in search results.  Formatted as a string with comma separated integers. |
| event_ids | string | Files need to be in the list of event ids to be included in search results.  Formatted as a string with comma separated integers. |
| tag_ids | string | Files need to be in the list of tag ids to be included in search results.  Formatted as a string with comma separated integers. |
| camera | string | Camera name from exif data to include in search results.  This is a keyword match as opposed to a full string match. |
| date_from | string | Start date in the format of YYYY-MM-DD.  Photos need to be taken after date_from to be included in search results. |
| date_to | string | End Date in the format of YYYY-MM-DD.  Photos need to be taken before date_to to be included in search results. |

### Status Codes

| Code | Reason |
|------|-------------|
| 200 | Request has succeeded. |

## POST /search/saved

Creates a new saved search

### Authorization

`user`

### Parameters

| Name | Type | Description |
|------|:----:|-------------|
| favorite | boolean | Indicator if the file is favorited (not yet implemented in client UI). |
| in_trash | boolean | Indicator if trash is to be searched (default false). |
| has_geodata | boolean | Indicator if geodata is required for the file to be included in the search results. |
| geo_lat | float | Geographic Latitude.  Requres geo_lon and geo_distance. |
| geo_lon | float | Geographic Longitude.  Requres geo_lat and geo_distance. |
| geo_distance | string | Geographic Distance.  Integer format followed by 'mi' or 'km' for miles or kilometers.  Requires geo_lat and geo_lon.  If Geographic coordinates are specified in the search criteria then the file will be included in the search results if it fits within the specified area. |
| album_ids | string | Files need to be in the list of album ids to be included in search results.  Formatted as a string with comma separated integers. |
| event_ids | string | Files need to be in the list of event ids to be included in search results.  Formatted as a string with comma separated integers. |
| tag_ids | string | Files need to be in the list of tag ids to be included in search results.  Formatted as a string with comma separated integers. |
| camera | string | Camera name from exif data to include in search results.  This is a keyword match as opposed to a full string match. |
| date_from | string | Start date in the format of YYYY-MM-DD.  Photos need to be taken after date_from to be included in search results. |
| date_to | string | End Date in the format of YYYY-MM-DD.  Photos need to be taken before date_to to be included in search results. |
| page | integer | Request page to allow for paged search results (default 1). |
| per_page | integer | Items per page (default 10). |

### Response

```JSON
{
  "id": integer,
  "name": string,
  "query": {
     "favorite": string,
     "in_trash": string,
     "has_geodata": string,
     "album_ids": string,
     "event_ids": string,
     "tag_ids": string,
     "camera": string,
     "date_from": string,
     "date_to": string,
     "geo": {
       "lat": float, 
       "lng": float,
       "distance": string
     }
  }
}
```

| Key | Type | Description |
|------|:----:|-------------|
| id | integer | Saved Search ID. |
| name | string | Saved Search name. |
| favorite | boolean | Indicator if the file is favorited (not yet implemented in client UI). |
| in_trash | boolean | Indicator if trash is to be searched (default false). |
| has_geodata | boolean | Indicator if geodata is required for the file to be included in the search results. |
| geo_lat | float | Geographic Latitude.  Requres geo_lon and geo_distance. |
| geo_lon | float | Geographic Longitude.  Requres geo_lat and geo_distance. |
| geo_distance | string | Geographic Distance.  Integer format followed by 'mi' or 'km' for miles or kilometers.  Requires geo_lat and geo_lon.  If Geographic coordinates are specified in the search criteria then the file will be included in the search results if it fits within the specified area. |
| album_ids | string | Files need to be in the list of album ids to be included in search results.  Formatted as a string with comma separated integers. |
| event_ids | string | Files need to be in the list of event ids to be included in search results.  Formatted as a string with comma separated integers. |
| tag_ids | string | Files need to be in the list of tag ids to be included in search results.  Formatted as a string with comma separated integers. |
| camera | string | Camera name from exif data to include in search results.  This is a keyword match as opposed to a full string match. |
| date_from | string | Start date in the format of YYYY-MM-DD.  Photos need to be taken after date_from to be included in search results. |
| date_to | string | End Date in the format of YYYY-MM-DD.  Photos need to be taken before date_to to be included in search results. |


### Status Codes

| Code | Reason |
|------|-------------|
| 200 | Request has succeeded. |

## PUT /search/saved/{searchId}

Update a saved search

### Authorization

`user`

### Parameters

| Name | Type | Description |
|------|:----:|-------------|
| searchId | integer | The ID of the saved search to update. |
| favorite | boolean | Indicator if the file is favorited (not yet implemented in client UI). |
| in_trash | boolean | Indicator if trash is to be searched (default false). |
| has_geodata | boolean | Indicator if geodata is required for the file to be included in the search results. |
| geo_lat | float | Geographic Latitude.  Requres geo_lon and geo_distance. |
| geo_lon | float | Geographic Longitude.  Requres geo_lat and geo_distance. |
| geo_distance | string | Geographic Distance.  Integer format followed by 'mi' or 'km' for miles or kilometers.  Requires geo_lat and geo_lon.  If Geographic coordinates are specified in the search criteria then the file will be included in the search results if it fits within the specified area. |
| album_ids | string | Files need to be in the list of album ids to be included in search results.  Formatted as a string with comma separated integers. |
| event_ids | string | Files need to be in the list of event ids to be included in search results.  Formatted as a string with comma separated integers. |
| tag_ids | string | Files need to be in the list of tag ids to be included in search results.  Formatted as a string with comma separated integers. |
| camera | string | Camera name from exif data to include in search results.  This is a keyword match as opposed to a full string match. |
| date_from | string | Start date in the format of YYYY-MM-DD.  Photos need to be taken after date_from to be included in search results. |
| date_to | string | End Date in the format of YYYY-MM-DD.  Photos need to be taken before date_to to be included in search results. |
| page | integer | Request page to allow for paged search results (default 1). |
| per_page | integer | Items per page (default 10). |

### Response

```JSON
{
  "id": integer,
  "name": string,
  "query": {
     "favorite": string,
     "in_trash": string,
     "has_geodata": string,
     "album_ids": string,
     "event_ids": string,
     "tag_ids": string,
     "camera": string,
     "date_from": string,
     "date_to": string,
     "geo": {
       "lat": float, 
       "lng": float,
       "distance": string
     }
  }
}
```

| Key | Type | Description |
|------|:----:|-------------|
| id | integer | Saved Search ID. |
| name | string | Saved Search name. |
| favorite | boolean | Indicator if the file is favorited (not yet implemented in client UI). |
| in_trash | boolean | Indicator if trash is to be searched (default false). |
| has_geodata | boolean | Indicator if geodata is required for the file to be included in the search results. |
| geo_lat | float | Geographic Latitude.  Requres geo_lon and geo_distance. |
| geo_lon | float | Geographic Longitude.  Requres geo_lat and geo_distance. |
| geo_distance | string | Geographic Distance.  Integer format followed by 'mi' or 'km' for miles or kilometers.  Requires geo_lat and geo_lon.  If Geographic coordinates are specified in the search criteria then the file will be included in the search results if it fits within the specified area. |
| album_ids | string | Files need to be in the list of album ids to be included in search results.  Formatted as a string with comma separated integers. |
| event_ids | string | Files need to be in the list of event ids to be included in search results.  Formatted as a string with comma separated integers. |
| tag_ids | string | Files need to be in the list of tag ids to be included in search results.  Formatted as a string with comma separated integers. |
| camera | string | Camera name from exif data to include in search results.  This is a keyword match as opposed to a full string match. |
| date_from | string | Start date in the format of YYYY-MM-DD.  Photos need to be taken after date_from to be included in search results. |
| date_to | string | End Date in the format of YYYY-MM-DD.  Photos need to be taken before date_to to be included in search results. |


### Status Codes

| Code | Reason |
|------|-------------|
| 200 | Request has succeeded. |

## DELETE /search/saved/{searchId}

Delete a saved search

### Authorization

`user`

### Parameters

| Name | Type | Description |
|------|:----:|-------------|
| searchId | integer | The ID of the saved search to update. |

### Status Codes

| Code | Reason |
|------|-------------|
| 204 | Request has succeeded. |

## GET /search/saved/{searchId}/files

Gets a list of files that match the saved search criteria.

### Authorization

`user`

### Parameters

| Name | Type | Description |
|------|:----:|-------------|
| searchId | integer | The ID of the saved search. |
| Page | integer | When retrieving multiple pages of files, this is the page number to retrieve |
| Per_Page | integer | When retrieving multiple pages of files, this is the number of files per page to retrieve (default 100) |

### Response

```JSON
{
  "total_count": integer,
  "items": {
             [
               {
                 "id": integer,
                 "name": "string",
                 "media": "string",
                 "bytes": integer,
                 "date_time_taken": "Date",
                 "size": {
                   "width": integer,
                   "height": integer
                 },
                 "exif": {
                   "exposure_comp": "string",
                   "shutter_speed": "string",
                   "flash": "string",
                   "metering_mode": "string",
                   "lens": "string",
                   "camera": "string",
                   "iso": "string",
                   "focal_length": "string",
                   "aperture": "string"
                 },
                 "gps": {
                   "lat": float, 
                   "lng": float
                 },
                 "thumbnails": {
                   "url": "string"
                   "s640": "string",
                   "s1280": "string",
                   "s2048": "string",
                 }
               }
             ]
           }
}
```

| Key | Type | Description |
|------|:----:|-------------|
| total_count | integer | Total number of items available regardless of Page_Size.  The 'items' set size is limited by the Page_Size |
| id | integer | File ID. |
| name | string | File name. |
| media | string | Full URL that can be used to download the file. |
| bytes | integer | Size of the file in bytes. |
| date_time_taken | Date | Date and time the file was taken. |
| size / width | integer | The file's width in pixels. |
| size / height | integer | The file's height in pixels. |
| exif | dictionary | The file's exif information as provided by the camera. |
| gps lat/lng | float | The location where the photo was taken or synchronized. |
| thumbnails / url | string | Base URL for the thumbnail; append file size to return specific sizes. |
| thumbnails / s640 | string | Returns a 640px thumbnail. |
| thumbnails / s1280 | string | Returns a 1280px thumbnail. |
| thumbnails / s2048 | string | Returns a 2048px thumbnail. |

### Status Codes

| Code | Reason |
|------|-------------|
| 200 | Request has succeeded. |