# DJI Thermal SDK Docker image (Output TIFF Version)

This solution utilizes the [Dockerized DJI Thermal SDK](https://github.com/daz/dji-thermal-sdk-docker) developed by [Darren Jeacocke](https://github.com/daz), but includes the additional feature of converting the output to TIFF files and preserving all metadata using [Exiftool by Phil Harvey](https://exiftool.org/).

We have added an example tool for processing and analyzing DJI thermal images. We share three images taken with a drone DJI Matrice 300 RTK + Thermal Camera H20T.

## Installation

```sh
docker build -t djithermal .                        
```

## Commands

```sh
docker run -i \
  djithermal \
  dji_irp --help
```

## Example of Use

### Measure temperature (raw float32 thermal) on all images in the current directory, converting to TIFF and copying exif metadata from the original file

Copy and paste this command in your bash terminal after intall and build the "djithermal" docker image.

```sh
docker run -i \
  -v "$(pwd)":"$(pwd)" -w "$(pwd)" \
  djithermal \
  /bin/sh -c 'mkdir -p raw tif 
  for i in *.JPG; do    
    dji_irp -a measure \
      --measurefmt float32 \
      --distance 25 \
      --humidity 77 \
      --emissivity 0.98 \
      --reflection 23 \
      --source "$i" \
      --output "$(pwd)/raw/$(basename $i .JPG).raw"
    raw2tiff -L -w 640 -l 512 -d float -c none -r 128 "$(pwd)/raw/$(basename $i .JPG).raw" "$(pwd)/tif/$(basename $i .JPG).tiff"
    exiftool -overwrite_original -TagsFromFile "$i" -all:all -xmp:xmp "$(pwd)/tif/$(basename $i .JPG).tiff"
    rm "$(pwd)/raw/$(basename $i .JPG).raw"
  done
  rm -r raw'
```

This command will create a "tif" folder where the output of TIFF files will be saved