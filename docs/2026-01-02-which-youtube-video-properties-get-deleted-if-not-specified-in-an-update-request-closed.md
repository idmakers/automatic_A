---
title: Understanding YouTube Video Property Updates
tags: python, youtube, api, update, video properties

---
# Core Problem
The YouTube Data API v3's `Videos.update` endpoint does not delete unspecified video properties when updating a video. This can lead to unexpected changes in the video data.

## Solution & Analysis

### Understanding YouTube API Documentation

According to the YouTube Data API documentation, if an update request is submitted without specifying a value for a property that already has a value, the property's existing value will be deleted. However, this does not always happen as observed in the example.

```python
import google_auth_oauthlib.flow
import googleapiclient.discovery
import pprint

# Set up YouTube authentication via OAuth
SCOPES = "https://www.googleapis.com/auth/youtube"
CLIENT_SECRETS_FILE = "client_secrets.json"

def setup_youtube_auth():
    flow = google_auth_oauthlib.flow.InstalledAppFlow.from_client_secrets_file(CLIENT_SECRETS_FILE, SCOPES)
    credentials = flow.run_local_server(port=0)
    youtube = googleapiclient.discovery.build("youtube", "v3", credentials=credentials)
    return youtube

# Get video information
def get_video_info(youtube, video_id):
    video_response = youtube.videos().list(
        part="snippet,status,recordingDetails",
        id=video_id,
    ).execute()
    return video_response["items"][0]

# Update video data
def update_video_data(youtube, video_id, updated_properties):
    video_update_response = youtube.videos().update(
        part="snippet,status,recordingDetails",
        body={
            "id": video_id,
            "snippet": {
                "title": updated_properties["snippet"]["title"],
                "categoryId": updated_properties["snippet"]["categoryId"],
            },
        }
    ).execute()
    return video_update_response

# Main function
def main():
    youtube = setup_youtube_auth()
    video_info = get_video_info(youtube, "QSC_ahDwuBs")

    # Update video data
    updated_properties = {
        "snippet": {
            "title": "test title",
            "categoryId": video_info["snippet"]["categoryId"],
        }
    }
    video_update_response = update_video_data(youtube, video_info["id"], updated_properties)

    print("Original video data:")
    pprint.pprint(video_info)

    print("\nNew video data:")
    pprint.pprint(video_update_response)

if __name__ == "__main__":
    main()
```

### Explanation

The issue arises because the `Videos.update` endpoint does not automatically delete unspecified properties. To fix this, we need to explicitly remove the properties from the update request body.

```python
def update_video_data(youtube, video_id, updated_properties):
    # Remove unspecified properties from the update request body
    for property_name in video_info["snippet"]:
        if property_name not in updated_properties["snippet"]:
            del updated_properties["snippet"][property_name]

    video_update_response = youtube.videos().update(
        part="snippet,status,recordingDetails",
        body={
            "id": video_id,
            "snippet": updated_properties["snippet"],
        }
    ).execute()
    return video_update_response
```

### Conclusion

To avoid unexpected changes in the video data, make sure to explicitly remove unspecified properties from the update request body. This ensures that only the specified properties are updated and leaves the rest unchanged.

<!-- ADSENSE_PLACEHOLDER -->