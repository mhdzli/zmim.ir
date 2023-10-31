---
title: FFmpeg and SOX
layout: wiki
date: 2022-03-15 08:10:47 +03:30
modified:
tags: [unix/linux, foss, ffmpeg, sox, media ]
description: ffmpeg and sox scripts.
---

# common usage

{% highlight bash %}
ffmpeg -i input.mp4 -hide_banner # getting input file information 

ffmpeg -i input.webm -qscale 0 output.mp4 # convert video preserving the quality of source video file

ffmpeg -formats # list all supported formats

ffmpeg -i input.mp4 -vn -ar 44100 -ac 2 -ab 320k -f mp3 output.mp3 # split audio vn: disable video. -ar audio frequency (common values 22050, 44100, 48000 Hz). -ac: number of audio channels. -ab: audio bitrate. -f: format. 

ffmpeg -i input.mp3 -af 'volume=0.5' output.mp3 # decrease volume by half (use numbers greater than 1 to increase volume)

ffmpeg -i input.mp4 -vf "setpts=0.5*PTS" output.mp4 # increase/decrease(0.5 double, 2 half) the video playback speed

ffmpeg -i input.mp4 -filter:a "atempo=2.0" -vn output.mp4 # Increase/decrease Audio playback speed (available value 0.5-2.0)

ffmpeg -i input.mp4 -filter:v scale=1280:720 -c:a copy output.mp4
ffmpeg -i input.mp4 -s 1280x720 -c:a copy output.mp4 # set a particular resolution

ffmpeg -i input.mp4 -vf scale=1280:-1 -c:v libx264 -preset veryslow -crf 24 -ac 2 -c:a aac -strict -2 -b:a 128k output.mp4 # reduce size and quality of output file

ffmpeg -i input.mp3 -ab 128 output.mp3 # reduce audio bitrate (available bitrates: 96, 112, 128, 160, 192, 256, 320kbps)

ffmpeg -i input.mp4 -an output.mp4 # remove audio

ffmpeg -i input.mp4 -vn output.mp3 # remove video

ffmpeg -i input.mp4 -r 1 -f image2 image-%2d.png # extract images -r: frame rate (image per second). -f: format. %2d name outputs image-01.png - image-99.png

ffmpeg -i input.mp4 -filter:v "crop=w:h:x:y" output.mp4 # crop video

ffmpeg -i input.mp4 -aspect 16:9 output.mp4 # set the aspect ratio (common ratios: 16:9, 4:3, 16:10, 5:4, 2:21:1, 2:35:1, 2:39:1)

ffmpeg -loop 1 -i inputimage.jpg -i inputaudio.mp3 -c:v libx264 -c:a aac -strict experimental -b:a 192k -shortest output.mp4 # add poster image to audio

ffmpeg -i input.mp4 -codec:v h264 -codec:a copy -ss 00:00:10.70 -t 01:10:52.26 output.mp4 # split a video from starting from 00:00:10.70 and end in 01:10:52.26

ffmeg -i vinput.mp4 -codec:v h264 -an -sn -dn -ss 00:05:00 -t 00:10:00 -i ainput.mp4 -codec:a mp3 -vn -ss 00:12:00 -t 00:17:00 output.mp4 # create output.mp4 using vinput.mp4 from 00:05:00 to 00:10:00 as video source and ainput.mp4 from 00:12:00 -t 00:17:00 as audio source

ffmpeg -f concat -safe 0 -i <PATH-TO-LIST> -c copy ouput
ffmpeg -i "concat:input1.mp4|input2.mp4|input3.mp4" -c copy output.mp4 # concatinating the files in list

fmpeg -i input.mp4 -i subtitle.srt -map 0 -map 1 -c copy -c:v libx264 -crf 23 -preset veryfast output.mp4
ffmpeg -i infile.mp4 -i infile.srt -c copy -c:s mov_text outfile.mp4 # add subtitle

ffmpeg -i input.mp4 -vf "transpose=1" output.mp4
ffmpeg -i input.mp4 -vf "transpose=clock" output.mp4 # rotate the given video by 90 degrees clockwise.

# 0. Rotate by 90 degrees counter-clockwise and flip vertically. This is the default.
# 1. Rotate by 90 degrees clockwise.
# 2. Rotate by 90 degrees counter-clockwise.
# 3. Rotate by 90 degrees clockwise and flip vertically.
{% endhighlight %}

# metadata

{% highlight bash %}
ffmpeg -i in.mp4 -f ffmetadata in.txt # Save the global metadata to a text file
ffmpeg -i in.mp4 -c copy -map_metadata 0 -map_metadata:s:v 0:s:v -map_metadata:s:a 0:s:a -f ffmetadata in.txt # Save metadata from the video and audio streams
ffprobe -show_streams -show_format input.mkv # Use ffprob for information
ffprobe -show_frames input.mkv # Use ffprobe or information about each single frame in a video file 
ffmpeg -i source.mp4 -metadata album_artist='VAR' -c copy output.mp4 # Set metadata
find /home/user/videos-to-process/ -type f -iname '*.mp4' -exec ffmpeg -i "{}" -codec copy -metadata artist="John Smith" -metadata album="Foo Bar" "{}.new.mp4" \; -exec mv "{}.new.mp4" "{}" \; # set metadata
{% endhighlight %}

# lbry preferred format

{% highlight bash %}
ffmpeg -i input.avi -c:v libx264 -crf 21 -preset faster -pix_fmt yuv420p -maxrate 5000K -bufsize 5000K -vf 'scale=if(gte(iw\,ih)\,min(1920\,iw)\,-2):if(lt(iw\,ih)\,min(1920\,ih)\,-2)' -movflags +faststart -c:a aac -b:a 160k output.mp4
{% endhighlight %}

# convert a PDF to video

{% highlight bash %}
convert -density 400 input.pdf picture.png # convert PDF to picture-1.png, ...

ffmpeg -r 1/10 -i picture-%01d.png -c:v libx264 -r 30 -pix_fmt yuv420p video.mp4 # -r 1/10: Display each image for 10 seconds.
{% endhighlight %}

# noise reduction using sox

{% highlight bash %}
#!/usr/bin/env bash

usage ()
{
    echo 'Usage : noiseclean.sh <input video file> <output video file>'
    exit
}

# Tests for requirements
ffmpeg -version >/dev/null || { echo >&2 "We require 'ffmpeg' but it's not installed. Install it by 'sudo apt-get install ffmpeg' Aborting."; exit 1; }
sox --version >/dev/null || { echo >&2 "We require 'sox' but it's not installed. Install it by 'sudo apt-get install sox' Aborting."; exit 1; }

if [ "$#" -ne 2 ]
then
  usage
fi

if [ ! -e "$1" ]
then
    echo "File not found: '$1'"
    exit
fi

if [ -e "$2" ]
then
    read -p "File '$2' already exists, overwrite? [y/N]: " yn
    case $yn in
        [Yy]* ) break;;
        * ) exit;;
    esac
fi

inBasename=$(basename "$1")
inExt="${inBasename##*.}"

isVideoStr=`ffprobe -v warning -show_streams "$1" | grep codec_type=video`
if [[ ! -z $isVideoStr ]]
then
    isVideo=1
    echo "Detected '$inBasename' as a video file"
else
    isVideo=0
    echo "Detected '$inBasename' as an audio file"
fi

read -p "Sample noise start time [00:00:00]: " sampleStart
if [[ -z $sampleStart ]] ; then sampleStart="00:00:00"; fi
read -p "Sample noise end time [00:00:00.500]: " sampleEnd
if [[ -z $sampleEnd ]] ; then sampleEnd="00:00:00.500"; fi
read -p "Noise reduction amount [0.21]: " sensitivity
if [[ -z $sensitivity ]] ; then sensitivity="0.21"; fi


tmpVidFile="/tmp/noiseclean_tmpvid.$inExt"
tmpAudFile="/tmp/noiseclean_tmpaud.wav"
noiseAudFile="/tmp/noiseclean_noiseaud.wav"
noiseProfFile="/tmp/noiseclean_noise.prof"
tmpAudCleanFile="/tmp/noiseclean_tmpaud-clean.wav"

echo "Cleaning noise on '$1'..."

if [ $isVideo -eq "1" ]; then
    ffmpeg -v warning -y -i "$1" -qscale:v 0 -vcodec copy -an "$tmpVidFile"
    ffmpeg -v warning -y -i "$1" -qscale:a 0 "$tmpAudFile"
else
    cp "$1" "$tmpAudFile"
fi
ffmpeg -v warning -y -i "$1" -vn -ss "$sampleStart" -t "$sampleEnd" "$noiseAudFile"
sox "$noiseAudFile" -n noiseprof "$noiseProfFile"
sox "$tmpAudFile" "$tmpAudCleanFile" noisered "$noiseProfFile" "$sensitivity"
if [ $isVideo -eq "1" ]; then
    ffmpeg -v warning -y -i "$tmpAudCleanFile" -i "$tmpVidFile" -vcodec copy -qscale:v 0 -qscale:a 0 "$2"
else
    cp "$tmpAudCleanFile" "$2"
fi

echo "Done"
{% endhighlight %}

# booksplit

{% highlight bash %}
#!/bin/sh

# Requires ffmpeg (audio splitting) and my `tag` wrapper script.

[ ! -f "$2" ] && printf "The first file should be the audio, the second should be the timecodes.\
" && exit

echo "Enter the album/book title:"; read -r booktitle

echo "Enter the artist/author:"; read -r author

echo "Enter the publication year:"; read -r year

inputaudio="$1"

# Get a safe file name from the book.
escbook="$(echo "$booktitle" | iconv -cf UTF-8 -t ASCII//TRANSLIT | tr -d '[:punct:]' | tr '[:upper:]' '[:lower:]' | tr ' ' '-' | sed "s/-\+/-/g;s/\(^-\|-\$\)//g")"

! mkdir -p "$escbook" && echo "Do you have write access in this directory?" && exit 1

# As long as the extension is in the tag script, it'll work.
ext="opus"
#ext="${1#*.}"

# Get the total number of tracks from the number of lines.
total="$(wc -l < "$2")"

while read -r x;
do
	end="$(echo "$x" | cut -d' ' -f1)"

	[ -n "$start" ] &&
	echo "From $start to $end; $track $title"
	file="$escbook/$(printf "%.2d" "$track")-$esctitle.$ext"
	[ -n "$start" ] && echo "Splitting \"$title\"..." &&
   	ffmpeg -nostdin -y -loglevel -8 -i "$inputaudio" -ss "$start" -to "$end" -vn -c copy "$file" &&
	echo "Tagging \"$title\"..." && tag -a "$author" -A "$booktitle" -t "$title" -n "$track" -N "$total" -d "$year" "$file"
	title="$(echo "$x" | cut -d' ' -f 2-)"
	esctitle="$(echo "$title" | iconv -cf UTF-8 -t ASCII//TRANSLIT | tr -d '[:punct:]' | tr '[:upper:]' '[:lower:]' | tr ' ' '-' | sed "s/-\+/-/g;s/\(^-\|-\$\)//g")"
	track="$((track+1))"
	start="$end"
done < "$2"
# The last track must be done outside the loop.
echo "From $start to the end: $title"
file="$escbook/$(printf "%.2d" "$track")-$esctitle.$ext"
echo "Splitting \"$title\"..." && ffmpeg -nostdin -y -loglevel -8 -i "$inputaudio" -ss "$start" -vn -c copy "$file" &&
		echo "Tagging \"$title\"..." && tag -a "$author" -A "$booktitle" -t "$title" -n "$track" -N "$total" -d "$year" "$file"
{% endhighlight %}

## tag script

{% highlight bash %}
#!/bin/sh

err() { echo "Usage:
	tag [OPTIONS] file
Options:
	-a: artist/author
	-t: song/chapter title
	-A: album/book title
	-n: track/chapter number
	-N: total number of tracks/chapters
	-d: year of publication
	-g: genre
	-c: comment
You will be prompted for title, artist, album and track if not given." && exit 1 ;}

while getopts "a:t:A:n:N:d:g:c:f:" o; do case "${o}" in
	a) artist="${OPTARG}" ;;
	t) title="${OPTARG}" ;;
	A) album="${OPTARG}" ;;
	n) track="${OPTARG}" ;;
	N) total="${OPTARG}" ;;
	d) date="${OPTARG}" ;;
	g) genre="${OPTARG}" ;;
	c) comment="${OPTARG}" ;;
	f) file="${OPTARG}" ;;
	*) printf "Invalid option: -%s\
" "$OPTARG" && err ;;
esac done

shift $((OPTIND - 1))

file="$1"

[ ! -f "$file" ] && echo "Provide file to tag." && err

[ -z "$title" ] && echo "Enter a title." && read -r title
[ -z "$artist" ] && echo "Enter an artist." && read -r artist
[ -z "$album" ] && echo "Enter an album." && read -r album
[ -z "$track" ] && echo "Enter a track number." && read -r track

case "$file" in
	*.ogg) echo "Title=$title
Artist=$artist
Album=$album
Track=$track
Total=$total
Date=$date
Genre=$genre
Comment=$comment" | vorbiscomment -w "$file" ;;
	*.opus) echo "Title=$title
Artist=$artist
Album=$album
Track=$track
Total=$total
Date=$date
Genre=$genre
Comment=$comment" | opustags -i -S "$file" ;;
	*.mp3) eyeD3 -Q --remove-all -a "$artist" -A "$album" -t "$title" -n "$track" -N "$total" -Y "$date" "$file" ;;
	*) echo "File type not implemented yet." ;;
esac
{% endhighlight %}

## use

{% highlight bash %}
booksplit path/to/audio path/to/timecodefile
{% endhighlight %}

## timecodefile:
{% highlight plaintext %}
00:00:00 first part name
hh:mm:ss second part name
...
{% endhighlight %}

# filters

## frozen frames with mpdecimate

{% highlight bash %}
ffmpeg -i input.mkv -vf mpdecimate,setpts=N/FRAME_RATE/TB -an output.mkv # remove frozen frames

ffmpeg -hide_banner -nostats -an -i $IN -vf "freezedetect=n=${NOISE}dB:d=${DURATION}" -f null - 2>&1 | grep -e "freeze_start" -e "freeze_end" # detect frozen frames 
{% endhighlight %}

## video stabliser vidstabs

{% highlight bash %}
ffmpeg -i input.mp4 -vf vidstabdetect -f null - # Use default values

ffmpeg -i input.mp4 -vf vidstabdetect=shakiness=10:accuracy=15:result="mytransforms.trf" -f null - # Analyzing strongly shaky video and putting the results in file mytransforms.trf

ffmpeg -i input.mp4 -vf vidstabdetect=show=1 dummy_output.mp4 # Visualizing the result of internal transformations in the resulting video

ffmpeg -i input.mp4 -vf vidstabdetect=shakiness=5:show=1 dummy_output.mp4 # Analyzing a video with medium shakiness

ffmpeg -i input.mp4 -vf vidstabtransform,unsharp=5:5:0.8:3:3:0.4 out_stabilized.mp4 # Using default values

ffmpeg -i input.mp4 -vf vidstabtransform=zoom=5:input="mytransforms.trf" out_stabilized.mp4 # Zooming-in a bit more and load transform data from a given file

ffmpeg -i input.mp4 -vf vidstabtransform=smoothing=30:input="mytransforms.trf" out_stabilized.mp4 # Smoothening the video even more
{% endhighlight %}