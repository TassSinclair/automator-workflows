# Automator Workflows
Clone this repository to interact with these Workflow objects properly, as MacOS will interpret the directories as Automator Workflow [Bundles](https://en.wikipedia.org/wiki/Bundle_(macOS)).

## Add cover from cover.jpg
Adds cover art as id3v2 tag to mp3 files. Select one or more mp3 files in Finder to use. Ensure "cover.jpg" file is in the same directory. Requires [FFmpeg](https://www.ffmpeg.org/) (install with `brew install ffmpeg`).
```bash
for f in "$@"
do 
  dir=`dirname "$f"`
  /usr/local/bin/ffmpeg -i "$f" -i "$dir/cover.jpg" \
    -map 0:0 -map 1:0 -c copy -id3v2_version 3 \
    -metadata:s:v title="Album cover" \
    -metadata:s:v comment="Cover (front)" "${f%.*}.tmp.mp3"
  mv "${f%.*}.tmp.mp3" "$f"
done
```

## Ask for resized image size
Raises a dialoge to ask what size we should use to create smaller copies of selected images. Used by [Make smaller copy](#make-smaller-copy).

## Autocrop
Crops empty space from the edges of image files. Select one or more image files in Finder to use. Requires [ImageMagick](https://www.imagemagick.org/) (install with `brew install imagemagick`).
```bash
for f in "$@"
do
  /usr/local/bin/convert $f -fuzz 1% -trim +repage $f
done
```

## Convert to FLAC
Converts audio files to [FLAC](https://en.wikipedia.org/wiki/FLAC). Select one or more audio files in Finder to use. Requires [SoX](http://sox.sourceforge.net/sox.html) (install with `brew install sox`).
```bash
for f in "$@"
do
  /usr/local/bin/sox "$f" "${f%.*}.flac"
done
```

## Convert to MP3
Converts audio files to MP3. Select one or more audio files in Finder to use. Requires [FFmpeg](https://www.ffmpeg.org/) (install with `brew install ffmpeg`).

```bash
for f in "$@"
do
  /usr/local/bin/ffmpeg -i "$f" \
    -vn -ar 44100 -ac 2 -ab 192k -f mp3 "${f%.*}.mp3"
done
```

## Make smaller copy
Makes smaller copies of image files. Select one or more image files in Finder to use. Uses [Ask for resized image](#ask-for-resized-image-size) to capture the desired maaximum length or width of the smaller copy. Requires [ImageMagick](https://www.imagemagick.org/) (install with `brew install imagemagick`).

```bash
size=`automator ~/Library/Services/Ask\ for\ resized\ image\ size.workflow`
for f in "$@"
do
    /usr/local/bin/convert "$f" -resize ${size}x${size} -quality 85 "${f%.*}-${size}.${f##*.}"
done
```