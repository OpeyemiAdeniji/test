# Table of Contents
- [The goal of this guide](#the-goal-of-this-guide)
- [Add a URL to your new resource's definition](#add-a-url-to-your-new-resources-definition)
- [Setup the action's frontend](#setup-the-actions-frontend)
- [Setup the action's backend](#setup-the-actions-backend)
- [Execute the action](#execute-the-action)
  - [Access the bucket's definition from Port](#access-the-buckets-definition-from-port)
- [Possible daily routine integrations](#possible-daily-routine-integrations)
- [Conclusion](#conclusion)



# Ingest YouTube Playlist Data into Port

This guide provides a step-by-step process to automate the ingestion of YouTube playlist data into Port using a GitHub workflow.

## Use Cases
- **Content Management**: Keep track of video details within your internal software catalog.
- **Analytics**: Integrate video data for better insights and decision-making.
- **Automation**: Automatically update your Port account with video and playlist metadata.

## Prerequisites
Before getting started, ensure you have the following:

- **Port Client ID and Secret**
- **YouTube API Key**
- **YouTube Playlist ID**
- **GitHub Repository** with the necessary permissions for workflows

## Creating Blueprints

### 1. Playlist Blueprint
Create a playlist blueprint in Port using the following JSON configuration:

<details>
<summary>Click to copy Playlist Blueprint JSON</summary>

```json
{
  "identifier": "playlist",
  "description": "This blueprint represents a YouTube playlist",
  "title": "playlist",
  "icon": "Widget",
  "schema": {
    "properties": {
      "playlistId": {
        "type": "string",
        "title": "Playlist ID"
      },
      "title": {
        "type": "string",
        "title": "Title"
      },
      "description": {
        "type": "string",
        "title": "Description"
      },
      "thumbnailUrl": {
        "type": "string",
        "title": "Thumbnail URL"
      },
      "videoCount": {
        "type": "number",
        "title": "Number of Videos"
      },
      "created_at": {
        "type": "string",
        "title": "Published At"
      }
    },
    "required": ["playlistId", "title"]
  }
}
```
</details>

### 2. Video Blueprint
Create a video blueprint in Port using the following JSON configuration:

<details>
<summary>Click to copy Video Blueprint JSON</summary>

```json
{
  "identifier": "video",
  "description": "This blueprint represents a video in our software catalog",
  "title": "video",
  "icon": "Widget",
  "schema": {
    "properties": {
      "videoId": {
        "type": "string",
        "title": "Video ID"
      },
      "title": {
        "type": "string",
        "title": "Title"
      },
      "description": {
        "type": "string",
        "title": "Description"
      },
      "thumbnailUrl": {
        "type": "string",
        "title": "Thumbnail URL"
      },
      "duration": {
        "type": "string",
        "title": "Duration"
      },
      "viewCount": {
        "type": "number",
        "title": "View Count"
      },
      "likeCount": {
        "type": "number",
        "title": "Like Count"
      },
      "commentCount": {
        "type": "number",
        "title": "Comment Count"
      }
    },
    "required": ["videoId", "title"]
  },
  "relations": {
    "belongs_to_playlist": {
      "title": "Belongs to Playlist",
      "target": "playlist",
      "required": false,
      "many": false
    }
  }
}
```
</details>

## Creating the Self-Service Action
Configure Port to trigger the GitHub workflow:

<details>
<summary>Click to copy Self-Service Action JSON</summary>

```json
{
  "title": "Ingest YouTube Playlist Data",
  "description": "Trigger ingestion of YouTube playlist data into Port",
  "icon": "Action",
  "type": "trigger",
  "parameters": {
    "playlistId": {
      "type": "string",
      "title": "YouTube Playlist ID",
      "required": true
    },
    "portContext": {
      "type": "string",
      "title": "Port Context Payload",
      "required": true
    }
  }
}
```
</details>

## Adding GitHub Secrets
In your GitHub repository, navigate to **Settings > Secrets and Variables > Actions** and add the following secrets:

- **`YOUTUBE_API_KEY`**: Your YouTube API Key.
- **`PORT_CLIENT_ID`**: Your Port Client ID.
- **`PORT_CLIENT_SECRET`**: Your Port Client Secret.

## GitHub Workflow Configuration
Create `ingest_data.yml` under `.github/workflows/` in your repository:

<details>
<summary>Click to copy Workflow YAML</summary>

```yaml
name: Update Port with YouTube Playlist Data
on:
  workflow_dispatch:
    inputs:
      playlistid:
        description: 'ID of the YouTube playlist'
        required: true
      port_context:
        description: 'Port context payload'
        required: true

jobs:
  update_port:
    runs-on: ubuntu-latest
    env:
      PORT_CLIENT_ID: ${{ secrets.PORT_CLIENT_ID }}
      PORT_CLIENT_SECRET: ${{ secrets.PORT_CLIENT_SECRET }}
      YOUTUBE_API_KEY: ${{ secrets.YOUTUBE_API_KEY }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install jq for JSON processing
        run: sudo apt-get install jq

      - name: Fetch and Process YouTube Data using Bash
        id: fetch_data
        env:
          PLAYLIST_ID: ${{ inputs.playlistid }}
        run: |
          set -e
          # Add the Bash script provided in your workflow for fetching and processing YouTube data
```
</details>

---

## Step 5: Visualize Data in Port

Leverage Port's [visualization](https://docs.getport.io/customize-pages-dashboards-and-plugins/dashboards/) tools for insights:

- **Playlist Metrics**: Display video count and total views.
- **Engagement Analysis**: Use dashboards to monitor likes and comments.

  <img width="1435" alt="image" src="https://github.com/user-attachments/assets/17684b56-3b9d-4b74-9cc4-37c7ee165db8">

---

## Let's Test It!
Navigate to your Port self-service page, select the action, provide the YouTube Playlist ID, and execute. This will trigger the GitHub workflow and ingest the data into Port.
