# Music

Importing custom music into the game is pretty simple, and only requires 3 simple tools to get started.

## Encoding XA Files

Music tracks in Kula World are stored in `.XA` files, which can be created with custom tracks to be played in the game. Each XA file stores **4** tracks, who's structures can be reference in the [tables section](/TABLES.md). To build your own custom XA file, you need 3 tools:

-   [ffmpeg](https://github.com/BtbN/FFmpeg-Builds/releases) - Download the latest release for your platform, only the `ffmpeg` executable inside the `bin` folder is required. This is a very common tool and will be used for converting audio files into a compatible `.wav` format before conversion.
-   [candyk-psx-tools](http://psx.arthus.net/sdk/candyk-psx-tools-win.zip) - Only the `psxavenc` and `xainterleave` executables are required, and will be used for converting compatible audio files into the `.XA` format.

After these are downloaded, you can put them into the same directory:

![directory-image](https://raw.githubusercontent.com/KulaWorkshop/Music/main/directory.png)

Now, let's get into the commands. For this example, we will be using the [Kula Quest demo soundtrack](https://youtu.be/uFph4DZQ6ys). Check to make sure your track's length is set properly in **ExePatch** (see [track length](#track-length) below for more information). After you have your audio file, which can be in any format, you need to convert it into a **16-bit ADPCM mono wav** file using **ffmpeg**. Open a command line in the current directory and run the following command:

`ffmpeg -i input.mp3 -acodec pcm_s16le -ac 2 -ar 44100 output.wav`
<br>

Replace `input.mp3` with your input audio file. This should create a suitable `.wav` file in the same directory, or wherever you chose your output path. Next, using `psxavenc`, we need to convert our `.wav` file into a single-track `.xa` file using the following command:

`psxavenc -f 37800 -t xa -b 4 -c 2 -F 1 -C 1 input.wav output.xa`
<br>

Replace `input.wav` with your `.wav` file, and `psxavenc` should create a `.xa` file. However, this is a **single track** XA file, not a **multi track** one that's required by the game, which we will create now. In this example, we are going to be replacing **HIRO's** music, which if we follow the structure above, we can see that it's the **second track** inside of `MUSIC_1.XA`. It's worth noting that you **cannot** replace tracks inside an existing XA file, you can only build one from scratch. So since we're targeting `MUSIC_1.XA`, we will also need to choose a track for **BONUS 2**, **MARS**, and **HELL** as well. Ideally, you want to pick a new track to replace these tracks as well, but in this example we will replace **all 4** of them with the same track.

Before building the multi track XA file, a text file describing the order of the tracks needs to be created. To keep things simple, your XA tracks should be placed inside of the same directory as the text file. Here's an example:

```
1 xa track1.xa 1 0
1 xa track2.xa 1 1
1 xa track3.xa 1 2
1 xa track4.xa 1 3
```

<sub>Example from _tracks.txt_</sub>

Replace `track1.xa`, `track2.xa`, etc. with the paths of the tracks you'd like to build the final XA with. In our example, we will be using the same track to replace all 4, so our text file would like this:

```
1 xa demo_quest.xa 1 0
1 xa demo_quest.xa 1 1
1 xa demo_quest.xa 1 2
1 xa demo_quest.xa 1 3
```

Now that we've created the structure, we can finally build our multi track XA file with this command:

`xainterleave 1 tracks.txt MUSIC_1.XA`

This tool will take the 4 tracks described in the text file, and build the final XA file that can be put inside of the game. In this example, we were editing the tracks inside `MUSIC_1.XA` because that's the XA file that also contains **HIRO's** track, so we can replace the game's `MUSIC_1.XA` with our own.

It is worth noting that when importing custom `.XA` files into any game, it's **strongly** recommended to use a tool like [mkpsxiso](https://github.com/Lameguy64/mkpsxiso) to build your game from scratch, instead of injecting the file with a tool such as psx-mode2. I demonstrate how to use mkpsxiso in my [randomizer tutorial](https://youtu.be/ebwn7yB1bUc).

## Track Length

~~Unfortunately, the game hardcodes the lengths of each track used in-game, meaning that if you import your own custom track over another, **your track should be the same length as the one you're replacing**.~~

As of **06/12/2023**, customizing track lengths is now possible! Using my new tool [ExePatch](https://github.com/KulaWorkshop/ExePatch), the lengths of each XA file inside the game's executable file can be modified! Simply open up the tool, and set the length of each track in seconds.
