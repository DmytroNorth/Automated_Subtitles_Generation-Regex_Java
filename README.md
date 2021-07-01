## Introduction
This is automated workflow that facilites subtitles generation from a video file with custom control. Steps include using:

* **Speech to text** recognition with YouTube
* **Text extraction** with regular expressions
* **Punctuation restoration** with [**Punctuator**](http://bark.phon.ioc.ee/punctuator)
* **Force alignment** of generated text to audio with [**Gentle**](http://gentle-demo.lowerquality.com)
* Custom **Java code** that utilizes generated forced aligned data along with preformatted text to output final `.srt` subtitles file.

## 1. Speech to text with YouTube
**1.1.** Compress video to 240px 100kbps (minimal settings) with **Apple Compressor**.

**1.2.** Privately upload to Youtube.

**1.3.** Wait for Youtube to autogenerate subtitles and download them in `.srt` format through YouTube settings or a tool like [**Downsub**](https://downsub.com)

## 2. Cleaning subtitles from timecode
**2.1.** Run **REGEX** to remove timecode.

```
FIND: \d*\n\d{2}:\d{2}:\d{2},\d{3} --> \d{2}:\d{2}:\d{2},\d{3}\n
REPLACE:
```

**2.2.** Run **REGEX** to remove line breaks and empty lines.

```
FIND: \n
REPLACE: “ ” space
```
**2.3** Run **REGEX** to remove double spaces. 

```
FIND: “  ” two spaces
REPLACE: “ ” space
```

**2.4** Run [**Punctuator**](http://bark.phon.ioc.ee/punctuator) to put punctuation.

**2.5** Run **Grammarly** to check grammar.

## 3. Edit subtitles
**3.1.** Run **REGEX** tool to put new sentence on a new line followed by two empty lines.

```
FIND: (\w{2}\. )
REPLACE: $1\n\n\n
```
**3.2.** Split sentences into subtitles and line in a text editor like **Atom**:

* by using vertical line that limits number of characters
* automatically by the number of characters and inserting empty line after two lines in a row.

## 4. Auto time-stamp subtitles
**4.1.** Run [**Gentle**](http://gentle-demo.lowerquality.com) to auto align text to audio.

**4.2.** Run `subtitles.jar` to combine generated forced aligned data along with preformatted text to output `final.txt` subtitles file:

**4.2.1** Download Subtitles folder from GIT. Save on your hard drive (on Desktop).

**4.2.2** Replace `transcript.txt` and `align.csv` files in downloaded Subtitles folder by your own files.

**4.2.3** Open **Terminal.app**.

**4.2.4** Go to Subtitles folder in Terminal. (Run Terminal command: `cd /Users/mac_name/Desktop/Subtitles`).

**4.2.5** Run Terminal coomand: `java -jar subtitles.jar`. This will output `final.txt` file with `.srt` formatting.

## 5. Import to video editor FinalCut Pro X
**5.1** Change file and name from `.txt` to `.srt`.

**5.2.** Import `.srt` file in online subtitle editor like [**Happyscribe**](https://www.happyscribe.com/subtitle-tools/online-subtitle-editor/free), and then export it as `.srt` to ensure proper formatting.

**5.3** Launch **FinalCut Pro** and select File -> Import -> Captions, then select `.srt` file to import subtitles into Video Editing software.
