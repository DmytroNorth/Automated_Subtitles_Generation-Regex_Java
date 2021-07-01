## Functionality
Takes `transcript.txt` and `align.csv` files from Subtitles folder and creates `final.txt` file

## How to run
1. Download Subtitles folder from GIT. Save on your hard drive (on Desktop)
2. Replace `transcript.txt` and `align.csv` files in downloaded Subtitles folder by your own files
3. Open `Terminal.app`
4. Go to Subtitles folder in Terminal. (Copy to Terminal command: `cd /Users/mac_name/Desktop/Subtitles`)
5. Copy to Terminal coomand: `java -jar subtitles.jar`
6. Enjoy!

## Found issues
1. Not matched words combinations have timecodes `00:00:00,000`
2. Not handled case if there are several matches of words combinations

## Notes
1. For Mac users only
