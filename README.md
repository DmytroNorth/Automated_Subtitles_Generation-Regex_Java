<img width="1280" alt="subs2" src="https://user-images.githubusercontent.com/20174979/124312409-1b676a80-db24-11eb-8526-8603da0a0405.png">

## Introduction
An automated workflow that generates timestamped subtitles from a video file with custom control. Steps include using:

* **Speech to text** recognition with YouTube
* **Text cleanup** with regular expressions
* **Punctuation restoration** with [**Punctuator**](http://bark.phon.ioc.ee/punctuator)
* **Force alignment** of generated text to audio with [**Kaldi**](https://www.kaldi-asr.org/doc/about.html) based tool [**Gentle**](https://lowerquality.com/gentle/)
* Custom **Java** script that utilizes generated forced aligned data with preformatted text to output final `.srt` subtitles file.

### Input
Any video file with audio such as `.mp4` and `.mov`.
### Output
Standard `.srt` subtitle file that can be imported in any video editing software or straight to Vimeo or YouTube in the following format:

```
1
00:00:00,001 ---> 00:00:09,370
Traumatic brain injuries
are extremely debilitating for most people.

2
00:00:09,940 ---> 00:00:13,010
In our practice,
we see many brain injuries

3
00:00:13,300 ---> 00:00:16,350
that have resulted from falls
and motor vehicle accidents.
```

## 1. Speech to text with YouTube
In this stage, we will upload a video file with audio to YouTube and auto-generate low-quality subtitles, which we will be substantially modifying and improving. 

**1.1.** Compress video to 240px 100kbps (minimal settings) with any video editing or compression software such as **Apple Compressor** to save time on upload and processing.

**1.2.** Privately upload to Youtube.

**1.3.** Wait for Youtube to autogenerate subtitles and download them in `.srt` format through YouTube settings or a tool like [**Downsub**](https://downsub.com).

### Issues with YouTube generated subtitles
* Text not broken down in a compact and logical way
* Missing punctuation.

**Example:**

```
1
00:00:00,001 ---> 00:00:09,370
traumatic brain injuries are extremely debilitating for most people in

2
00:00:09,940 ---> 00:00:11,020
our practice

3
00:00:11,340 ---> 00:00:13,010
we see many brain 

4
00:00:13,300 ---> 00:00:14,230
injuries that have resulted
```
Nevertheless, this will give us a starting point. 

## 2. Cleaning subtitles from timecode

In this stage, we will remove timecode, line breaks, and double spacing.

**2.1.1.** Run **regex** to remove timecode.

```
FIND: \d*\n\d{2}:\d{2}:\d{2},\d{3} --> \d{2}:\d{2}:\d{2},\d{3}\n
REPLACE:
```

**2.1.2.** Run **regex** to remove line breaks, empty lines, and replace them with space.

```
FIND: \n
REPLACE: “ ” space
```
**2.1.3** Run **regex** to remove double spaces and replace them with single space. 

```
FIND: “  ” two spaces
REPLACE: “ ” space
```

After cleanup all we have is a sequence of words separated by space:

```
traumatic brain injuries are extremely debilitating
for most people in our practice we see many brain
injuries that have resulted from falls and motor
vehicle accidents
```

**2.2** Now let us run [**Punctuator**](http://bark.phon.ioc.ee/punctuator) online tool to automatically put punctuation and capitalize sentences:

```
Traumatic brain injuries are extremely debilitating
for most people. In our practice we see many brain
injuries that have resulted from falls and motor
vehicle accidents.
```


**2.3** And let us run [**Grammarly**](https://www.grammarly.com) to check for potential grammar, spelling, and punctuation issues.

After the clean-up process, we have punctuated and capitalized text without any timecode, double spaces, and line breaks. 

## 3. Edit subtitles
Next, let us split the text into logical subtitle chunks and prepare it for automatic timestamping.

**3.1.** Run **regex** to put every sentence on a new line followed by two empty lines.

```
FIND: (\w{2}\. )
REPLACE: $1\n\n\n
```
**Result:**

```
Traumatic brain injuries are extremely debilitating for most people. 


In our practice, we see many brain injuries that have resulted from falls and motor vehicle accidents.
```

**3.2.** Now let's split sentences into subtitle chunks. 

Though it can be done automatically with **regex** based on the number of characters or words I prefer to do it manually in a text editor like [**Atom**](https://atom.io) to have full control and ensure the highest quality of the end result.

**Result:**

```
Traumatic brain injuries
are extremely debilitating for most people.

In our practice,
we see many brain injuries

that have resulted from falls
and motor vehicle accidents.
```
Once we are done, let's rename it to `transcript.txt`. This will be one of the input files for our custom `subtitles.jar` script in the next stage.

## 4. Auto time-stamp subtitles
In this stage we will use an automatic forced aligner online tool, that will timestamp every word based on provided audio. After that, we will use a custom **Java** script to pull only the timestamps of the words at the beginning and the end of each subtitle chunk and structure it in a standard `.srt` subtitle format. 

**4.1.** Let us run [**Gentle**](https://lowerquality.com/gentle/) to auto-align text to audio. First, we upload the audio file along with the text. The tool will generate `align.csv` file in the following format:

|Original    |Aligned     |Start|End|
|------------|------------|----|----|
|Traumatic   |traumatic   |5.23|5.68|
|brain       |brain       |5.68|6.0 |
|injuries    |injuries    |6.0 |6.59|
|are         |are         |6.9 |7.02|
|extremely   |extremely   |7.02|7.75|
|debilitating|debilitating|7.76|8.57|

**4.2.** Now let us run `subtitles.jar` to combine generated forced aligned data along with preformatted text to output the final `.srt` subtitles file.

**4.2.1** Download `Subtitles` folder from this Github repository. Save locally on your Desktop.

**4.2.2** Replace `transcript.txt` and `align.csv` files in the downloaded `Subtitles` folder with the files we have prepared in the previous stages.

**4.2.3** Open **Terminal.app**.

**4.2.4** Go to `Subtitles` folder in Terminal by
running Terminal command: `cd /Users/insert_your_user_name/Desktop/Subtitles`.

**4.2.5** Run Terminal command: `java -jar subtitles.jar`

It will (finally!) output the `final.txt` file with `.srt` formatting.

```
1
00:00:00,001 ---> 00:00:09,370
Traumatic brain injuries
are extremely debilitating for most people.

2
00:00:09,940 ---> 00:00:13,010
In our practice,
we see many brain injuries

3
00:00:13,300 ---> 00:00:16,350
that have resulted from falls
and motor vehicle accidents.
```


## 5. Import to video editor FinalCut Pro X
In this stage, we simply rename `.txt` to `.srt` and run through an online tool to avoid possible issues upon importing into video editing software.

**5.1** Change file and name from `.txt` to `.srt`.

**5.2.** Import `.srt` file in online subtitle editor like [**Happyscribe**](https://www.happyscribe.com/subtitle-tools/online-subtitle-editor/free) and then export it as `.srt` to ensure proper formatting.

**5.3** Launch **FinalCut Pro** and select File -> Import -> Captions, then select `.srt` file to import subtitles into Video Editing software.

## Conclusion
Although it took us multiple steps and several online tools, in the end, we did manage to generate properly formatted subtitles from a raw video file with audio with a high degree of control. It can be huge time savings in a video production environment with tight deadlines.

There are multiple tools and software out there that have similar functionality. The best alternative I could find is [MacCaption by Telestream](https://www.telestream.net/captioning/overview.htm) with the prices starting at **$1,898**.

By following these guidelines it can be done quickly and efficiently for free.
