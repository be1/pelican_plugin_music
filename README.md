# Music Pelican plugin

Use `music` to add a musical track or an album to an article with pelican. Music tracks are copied from an organized bucket of albums (as directories) you want to relate to in your pages.

## Demo

You can see a demo pelican generated article here:
http://noise.rocks/~ben/parler.html

## How to install and configure

The plug-in requires `Tagpy`: a Python library for manipulating audio tags, which installation is outside the scope of this document.

The plug-in scans the referred tracks, and generates html presentation for albums and tracks with audio, based on the following configuration:

`MUSIC_LIBRARY = "~/your_music_directory"`
:	Absolute path to the folder where the original music tracks are kept, optionally organized in sub-folders (mandatory if you publish album).

`MUSIC_COVER = cover.jpg`
:	For music in albums or playlist folders, album cover image filename for the album or the playlist. The cover file would typically be a generic filename such as *cover.jpg*.

The plug-in automatically creates two objects types that you can use in your template for each album (or playlist) folder, taking information from each audio track inside it and publishes the audio tracks folders to the following output folder:

```
    ./output/music
```

The plug-in creates tag cache files in the `MUSIC_LIBARY` directory, therefore, album and tracks information are only done once. If you want to regenerate tags, just remove the *.tags* files from your music library.

## How to use

If you publish albums or playlists (and you certainy may want to), you must maintain an organized library of audio tracks **sorted by their filename** somewhere on disk, using folders to *group tracks* of the same album or same playlist (or topic).

* To get a single object `music_album` usable in your template, add the metadata field `album: {music}/folder{Optional Name}` to an article. This will scan for the designated folder. An article **must not** refer to multiple albums.
* To get a single object `music_track` usable in your template, add the metadata field `track: {music}/folder/track.ogg` to an article. This will scan for the designated audio file. You **must not** refer to multiple tracks. Use an `album:` header instead to refer to multiple tracks sorted by filename in the designed folder.

Here is an example Markdown article that shows all use cases:

	title: My Best Compositions
	album: {music}/favorites{Favorites}   <= an album (music_album available)
	track: {music}/favorites/featured.ogg <= a track (music_track available)

	Here are my *best* compositions, played with my favorite synth:
	[Aria]({music}/firstalbum/aria.ogg).
	[Sonata]({music}/secondone/sonata.ogg).

    your Jinja template can display the full album and cover, and the specific track, all with HTML5 <audio> markups (see Demo).

## How to change the Jinja templates

The plugin provides the following variables to your templates:

`article.music_track`
:	For articles with an associated track, a dict with the following information:

* The artist of the track. (from tag)
* The album of the track. (from tag)
* The title of the track. (from tag)
* The track number of the track (from tag)
* The year of the track (from tag)
* The path of the default cover image.
* The filename of the original track.

For example, modify the template `article.html` as shown below to display the associated track cover before the article content:

```html
<div class="entry-content">
	{% if article.music_track %}<img src="{{ SITEURL }}/{{ article.music_track.cover }}" />{% endif %}
	{% include 'article_infos.html' %}
	{{ article.content }}
</div><!-- /.entry-content -->
```

`article.music_album`
:	For articles with an album or a playlist, a dict of the album information is available with a title field and a tracks field having a list of tracks as shown above.

For example, add the following to the template `article.html` to add the album as the end of the article:

```html
{% if article.music_album %}
<div class="album">
		<h1>{{ album.title }}</h1>
		<ol>
		{% for track in album.tracks %}
			<li>
				<a href="{{ SITEURL }}/{{ track.path }}" title="{{ track.title }}">{{ track.title }}</a><audio src="{{ SITEURL }}/{{ track.path }}">
			</li>
		{% endfor %}
		</ol>
</div>
{% endif %}
```

For example, add the following to the template `index.html`, inside the `entry-content`, to display the cover with a link to the article:

```html
{% if article.music_track %}<a href="{{ SITEURL }}/{{ article.url }}"><img src="{{ SITEURL }}/{{ article.music_track.cover }}"
	style="display: inline; float: right; margin: 2px 0 2ex 4ex;" /></a>
{% endif %}
```
