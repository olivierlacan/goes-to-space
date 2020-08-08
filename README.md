# GOES to Space

## Installation

```
brew install wget
brew install imagemagick
brew install ffmpeg
```

## Usage

Source URL for full disk GOES-East:
- https://cdn.star.nesdis.noaa.gov/GOES16/ABI/FD/GEOCOLOR/

### Download full disk images for all available recent days
```
wget --execute="robots = off" --no-parent --recursive --convert-links \
--timestamping \
--accept '*-10848x10848.jpg' \
https://cdn.star.nesdis.noaa.gov/GOES16/ABI/FD/GEOCOLOR/
```

#### For a specific day

Where the day is number 214 (August 1st, 2020):
```
wget --execute="robots = off" --no-parent --recursive --convert-links \
--timestamping \
--accept '2020214*_GOES16-ABI-FD-GEOCOLOR-10848x10848.jpg' \
https://cdn.star.nesdis.noaa.gov/GOES16/ABI/FD/GEOCOLOR/
```

This will only download images for that specific day.

### Crop imagesto a 4K (3840x2160) around the carribbean

```
mkdir cropped/
mogrify -path cropped -crop 3840x2160+4200+1800 *_GOES16-ABI-FD-*.jpg
```

If you want to only crop specific days, use the day number (since the beginning
of the year) which is how NOAA names their files. So a filename of `20202140620`
represents `2020`, day `214`, and hour `0620` (in UTC/GMT).
```
mogrify -path cropped -crop 3840x2160+4200+1800 2020214*_GOES16-ABI-FD-*.jpg
```

### Convert cropped images to a 4K video

Using 60 frames per second because GOES-East imagery is updated every 10 seconds
which means 1 second of footage now becomes one hour in real life.
```
ffmpeg -r 60 -f image2 -pattern_type glob -i 'cropped/*.jpg' -vcodec libx264 -crf 25 -pix_fmt yuv420p output.mp4
```
