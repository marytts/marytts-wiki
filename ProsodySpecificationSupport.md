# Support for explicit prosody specifications

MARY TTS support explicit prosody specification using `prosody` element as described in [W3C Speech Synthesis Markup Language (SSML) 1.1 recommendation](http://www.w3.org/TR/speech-synthesis11/#edef_prosody); and the different attributes in `prosody` element like `rate`, `pitch` and `contour` are used as specifications to modify predicted phone durations and pitch contour before passing them to synthesizer.

**Note:** The following examples will only work if the `INPUT_TYPE` is set to `RAWMARYXML`. HSMM voices are able to realize the specified prosody out of the box; for unit selection voices, at least MARY 4.3.0 is needed, and the checkbox "Apply prosody modification" must be activated in the [web interface](http://mary.dfki.de:59125).

Example:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<maryxml version="0.4"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns=" http://mary.dfki.de/2002/MaryXML"
 xml:lang="en-US">
  <p>
    <prosody rate="fast" pitch="+10%" contour="(10%,low)(50%,-20%)(80%,+10%)(100%,+5st)">
      Welcome to the world of speech synthesis!
    </prosody>
  </p>
</maryxml>
```

## Adjusting Speech Rate

Example 1:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<maryxml version="0.4"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns=" http://mary.dfki.de/2002/MaryXML"
 xml:lang="en-US">
  <p>
    <prosody rate="200%">
      I am now speaking twice as fast.
    </prosody>
  </p>
</maryxml>
```

Example 2:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<maryxml version="0.4"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns=" http://mary.dfki.de/2002/MaryXML"
 xml:lang="en-US">
  <p>
    <prosody rate="70%">
      I am now speaking 30% more slowly.
    </prosody>
  </p>
</maryxml>
```

## Adjusting Pitch level

Example 1:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<maryxml version="0.4"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns=" http://mary.dfki.de/2002/MaryXML"
 xml:lang="en-US">
  <p>
    <prosody pitch="low">
      I am now speaking with 3/4 pitch.
    </prosody>
  </p>
</maryxml>
```

Example 2:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<maryxml version="0.4"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns=" http://mary.dfki.de/2002/MaryXML"
 xml:lang="en-US">
  <p>
   <prosody pitch="-5st">
     This is five semitones lower.
   </prosody>
  </p>
</maryxml>
```

## Shaping intonation contour

Example 1:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<maryxml version="0.4"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns=" http://mary.dfki.de/2002/MaryXML"
 xml:lang="en-US">
  <p>
    <prosody contour="(0%,x-low)(30%,low)(50%,medium)(70%,high)(100%,x-high)">
      I am talking with rising intonation.
    </prosody>
  </p>
</maryxml>
```

Example 2:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<maryxml version="0.4"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns=" http://mary.dfki.de/2002/MaryXML"
 xml:lang="en-US">
  <p>
    <prosody contour="(0%,+5st)(30%,+2st)(50%,+0.1st)(70%,-3st)(100%,-6st)">
      I am talking with falling intonation.
    </prosody>
  </p>
</maryxml>
```
