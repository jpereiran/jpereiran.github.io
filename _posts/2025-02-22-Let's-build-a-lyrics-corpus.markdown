---
title:  "Let's build a song lyrics dataset using various APIs"
date:   2025-02-22 18:45:16
categories: articles
abstract: Have you ever wanted to analyze trends in songwriting? Since my favorite artist dropped a new album, I had an idea—why not build a dataset of song lyrics? In this guide, I'll show you how to fetch lyrics from various APIs and create your own lyrics corpus [...]
---

### Introduction

Since my favorite artist released just realeased a new album, I started wondering if I could classify their albums based on song lyrics. Lyrics are a powerful way to tell stories, express emotions, and reflect cultural trends. So why not build a dataset to explore all of this?

In this article, we'll walk through how to create a song lyrics corpus using different APIs. We'll cover API selection, data fetching, processing, and storage, with code snippets along the way.

### Choosing the Right APIs

There are several APIs that can give you access to song lyrics and metadata, each with different features and limitations. Depending on your needs, some APIs are better suited for retrieving lyrics, while others excel at providing metadata or song structure information. Here’s a breakdown of some popular options:

#### Metadata APIs

- **Spotify API** – Spotify's API is an excellent resource for retrieving track metadata, album details, popularity metrics, and audio features like tempo, key, and danceability. However, you need to register to get access.
- **MusicBrainz API** – A free, open-source music database that provides extensive metadata, including artist discographies, album releases, and track listings. While it doesn't provide lyrics, it’s invaluable for organizing and structuring a lyrics dataset.

#### Lyrics APIs

- **Genius API** – One of the most popular sources for song lyrics and metadata. It provides song lyrics, artist details, and annotations that explain the meaning behind lyrics. However, direct access to full lyrics requires additional scraping or permissions.
- **Lyrics.ovh** – A free and simple API that allows users to retrieve lyrics without authentication. While its dataset isn't as extensive as other services, it’s a great option for quick access to lyrics without complex authentication processes.
- **ChartLyrics API** – A free lyrics database with a large collection of lyrics. However, it lacks active maintenance, which can lead to inconsistencies or missing lyrics.

Since our song lyrics dataset requires a two-step process—first retrieving a list of songs by an artist and then fetching the lyrics for each song—we will use the MusicBrainz API for the first step and Lyrics.ovh for the second. These APIs were chosen because they are free, open, and easy to use.

### Retrieving the Records

To begin, we need to understand how the MusicBrainz API works. You can explore the documentation [here](https://musicbrainz.org/doc/MusicBrainz_API), but a helpful reference is this image: [MusicBrainz Entity Network](https://wiki.musicbrainz.org/images/a/a7/entity_network_overview.svg). 

This diagram shows that to get all the songs of an artist, we first need to retrieve all their release groups, then find a specific release from a group, and finally extract the tracklist from that release.

To avoid retrieving unnecessary data, we need to filter the responses effectively. Below are the functions to achieve this:

#### Get all the release groups from an artist

```python
import requests
import pandas as pd

def get_release_group_from_artist(release_group_id):

    releases_url = f'https://musicbrainz.org/ws/2/release-group?artist={artist_id}&fmt=json'
    release_response = requests.get(releases_url)
    release_data = release_response.json()

    release_groups = []

    if 'release-groups' in release_data:
        for release_group in release_data['release-groups']:
            release_type = release_group['primary-type']
            secondary_type = release_group['secondary-types']

            # Only print albums (primary_type = "Album", secondary_type = [])
            if release_type == "Album" and secondary_type == []:
                release_groups.append({
                    'title': release_group['title'],
                    'release_date': release_group['first-release-date'],
                    'release_group_mbid': release_group['id']
                })

    
    return release_groups
```

#### Get all the releases from a release group

```python
def get_releases_from_group(release_group_id):
    url = f"https://musicbrainz.org/ws/2/release-group/{release_group_id}?inc=releases&fmt=json"
    response = requests.get(url)
    data = response.json()

    releases = []
    for release in data['releases']:
        releases.append({
                'title': release['title'],
                'mbid': release['id']
            })
    
    return releases

```

#### Get all the tracks from a release

```python
def get_tracks_from_release(release_id):
    url = f"https://musicbrainz.org/ws/2/release/{release_id}?inc=recordings&fmt=json"
    response = requests.get(url)
    data = response.json()
    
    tracks = []
    for track_entry in data['media'][0]['tracks']:
        tracks.append({
                    'title': track_entry['title'],
                    'position': track_entry['position'],
                    'length': track_entry['length']
                })
    
    return tracks
```

#### Get lyrics for a track

```python
def get_lyrics_from_track(artist, track):
    url = f"https://api.lyrics.ovh/v1/{artist}/{track}"
    response = requests.get(url)
    if response.status_code == 200:
        lyrics = response.json().get("lyrics")
        if lyrics is not None:
            lyrics = lyrics.replace('\n\n','\n')
    else:
        lyrics = ''
    return lyrics
```

### Processing & Storing the Data

Now that we have defined our functions, we can put everything together. Since I plan to analyze this data later, I will save it as an `.xlsx` file. Here is an example with my favorite artist.

```python
df_lyrics = pd.DataFrame(columns=['Artist', 'Artist ID', 'Release Group', 'Release Group Date', 'Release Group ID', 'Release', 'Release ID', 'Track', 'Track Position', 'Length', 'Lyrics'])

list_lyrics = []

artist = "Manic Street Preachers"
artist_id = '32efea44-6cb5-4b4f-bdaa-c8b8f6cef981' #we can get this id directly from the MusicBrainz website or create a new function to get it by the name of the artist

release_groups = get_release_group_from_artist(artist_id)

for release_group in release_groups:
    release_group_mbid = release_group['release_group_mbid']
    releases = get_releases_from_group(release_group_mbid)
    release_id = releases[0]['mbid']
    tracks = get_tracks_from_release(release_id)
    for track in tracks:
        track_lyrics = get_lyrics_from_track(artist, track['title'])
        list_lyrics.append([artist, artist_id, release_group['title'], release_group['release_date'], release_group_mbid, releases[0]['title'], release_id, track['title'], track['position'], track['length'], track_lyrics])

df_lyrics = pd.DataFrame(list_lyrics, columns=df_lyrics.columns)
df_lyrics.to_excel("DF_Artist_Lyrics.xlsx", sheet_name='Lyrics', index=False)
```

The full code used in this example can be found in the [blog's github](https://github.com/jpereiran/jpereiran-blog/tree/master/code/requests).


### Conclusion

Building a song lyrics corpus using APIs is a powerful approach for gathering data for analysis. By selecting the right APIs, handling data efficiently, and overcoming challenges, you can create a robust dataset for various applications. Whether you're interested in linguistic analysis, sentiment trends, or simply exploring your favorite artist's discography, this workflow provides a solid foundation for future research and experimentation.

