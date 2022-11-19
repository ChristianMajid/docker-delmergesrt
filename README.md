# docker-mergesrt
Fork of zzeneg/mergesrt. Docker container for monitoring subtitle files and merging them into video

   Published on [Docker Hub townsste](https://hub.docker.com/r/townsste/mergesrt)

   Published on [Docker Hub zzeneg](https://hub.docker.com/r/zzeneg/mergesrt)

## Features
- search for existing `*.lang.srt` files
- monitor for new `*.lang.srt` files
- merge subtitles to `mkv`/`mp4` files
- adds on to existing subtitles. thus allows for multiple subtitles to be added 
- remove merged subtitle files
- send notification to a webhook after a merge (success/fail)

## Added functionality
- Use 2 language code.
- Supports tags with sdh, forced, hi, & cc.
- Supports merging .idx with .sub file.
- Supports merging .ass
- Supports the tag being before or after the lang for Bazarr label scheme.

## Subtitle Format:
     file_name.LANGUAGE.TAG.srt or .ass
     file_name.TAG.LANGUAGE.srt or .ass
     file_name.LANGUAGE.srt or .ass
     file_name.idx

- Example:
     - SRT | ASS
          - file_name.en.sdh.srt or .ass
          - file_name.hi.en.srt or .ass
          - file_name.eng.forced.srt or .ass
          - file_name.eng.srt or .ass
          - file_name.en.srt or .ass

    - IDX
         - file_name.idx

## Requirements
- A folder with media files should be mapped to `/data`
- SRT or ASS file should have a language in its name in [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) or [ISO 639-2](https://en.wikipedia.org/wiki/List_of_ISO_639-2_codes) format, e.g. `name.en.srt or .ass` or `name.eng.srt or .ass`.
- For IDX a `.sub` file should be placed in the same directory as the `.idx` and `video file`.

## Usage
- specify environment variables for a webhook (optional): 
- PLEASE NOTE: I do not know how my changes effect the WEBHOOK aspect of this.
  - `WEBHOOK_URL` - URL for the POST request. For example you can use [Apprise](https://github.com/caronc/apprise)
  - `WEBHOOK_TEMPLATE` - template for the POST request body. Note that all double quotes `"` should be escaped by a backslash `\` and dollar signs `$` should be doubled. You can use variables in this template:
    - `$IMPORT_FILE` - full path to the subtitle file
    - `$VIDEO_FILE` - full path to the video file
    - `$EXT` - file extenstion
    - `$TYPE` - file type
    - `$LANG` - ISO 639-1 or ISO 639-2 language code of the subtitles 
    - `$RESULT` - merge result, returns `merge succeeded`, `merge completed with warnings` or `merge failed`

- docker-compose example:
 
  ```yaml
  mergesrt:
    container_name: mergesrt
    image: townsste/mergesrt
    restart: unless-stopped
    environment:
      WEBHOOK_URL: http://apprise:8000/notify
      WEBHOOK_TEMPLATE: '{\"title\":\"*MergeSRT - $$RESULT*\", \"body\":\"$$IMPORT_FILE\"}'
    volumes:
      - /media:/data
  ```
      
- A logo for the docker can be [found here](https://raw.githubusercontent.com/townsste/docker-templates/master/townsste/images/mergesrt.png)
