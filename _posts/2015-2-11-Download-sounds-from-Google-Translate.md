---
layout: post
title: Download sounds from Google Translate
---

Sometimes I need a robotic voice saying things.
A quick and easy way to get a sound file with the pronunciation of virtually any text is by using Google Translate's text-to-speech API.
Here is ruby code that achieves this. **NB** will not work on Windows, OSX, Linux only!

```ruby
require 'uri'
def wget(path: "", locale: "", phrase: "")
  if path == "" || locale == "" || phrase == ""
    puts "Arguments required:\npath - where to put the downloaded sound\nlocale - string version, i.e. \"lv\"\nphrase - vanilla text, i.e.\"Bādass test phrāše\""
  else
    escaped_phrase = URI::encode(phrase.strip.gsub(/\s+/,'+'))
    url = "http://translate.google.com/translate_tts?ie=UTF-8&tl=#{locale}&q=#{escaped_phrase}"
    puts "Getting sound for #{phrase} in #{locale}, using\n#{url}"

    %x|wget -q -U Mozilla -O #{path} "#{url}"|
  end
end
```

Place the code in speech.rb, open irb (or pry), require the file by typing

```ruby
require '~/path/to/speech.rb'
```

Execute the wget method with three valid parameters, for example:

```ruby
wget(path: "~/Desktop/download.mp3", locale: "en", phrase: "Y'all need to use this code!")
```

This will download an mp3 file to your desktop.

![robot.gif]({{ site.baseurl }}/images/robot.gif)
