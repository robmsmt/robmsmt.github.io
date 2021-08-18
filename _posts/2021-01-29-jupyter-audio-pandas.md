---
layout: post
title: Audio from inside a Pandas Dataframe in a Jupyter Notebook
---
Jupyter notebooks are fantastic tools to explore data. You can also play audio within them.

<div class="message">
  In order to make ASR better you often have to review and listen to many audio files.
  One great way I've found to do this is through a Pandas dataframe in a Jupyter notebook.
</div>


![image of commandline](../../../../public/images/audio_play.png)

As you can see from the picture above, it keeps the audio component hidden until you click on the wav
filename which triggers some javascript to make the audio HTML components visible. Kinda neat.

____

Within Jupyter notebooks you can use the [magic tags](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html) `%%html` in which are you able to define some javascript and CSS. See segment below:
{% highlight html %}
%%html
<script>

  function toggle_visibility(i)
  {
      if (i.style.display === "none") {
          i.style.display = "block";
      } else {
          i.style.display = "none";
      }
  }
</script>

<style>
  .filenameclass{
      cursor: pointer;
      text-decoration: underline blue;
      text-underline-position: under;
      color: blue;
  }
  audio{
      width: 200px;
  }
</style>
{% endhighlight %}

Next we just need to define a function for creating the HTML with the new audio widget in, this takes a path and returns some HTML with a special unique ID we can use to identify the component:
```python
def path_to_audio_html(path):
    myuuid = 'x'+str(uuid4())[:8]
    filename = path.rsplit('/')[-1]
    return f'''
    <div>
        <div class='filenameclass' onclick="toggle_visibility({myuuid})">{filename}</div>
        <div id="{myuuid}" style="display:none;">
            <audio controls>
              <source src="{path}" type="audio/wav" preload="none">
                Your browser does not support the audio element.
            </audio>
        </div>
    </div>
    '''
```
Assuming we already have a dataframe (with column to filepath), we just need to call the HTML function from `IPython.core.display`:
```python
HTML(df.to_html(escape=False,formatters=dict(filename=path_to_audio_html)))
```

![image of commandline](../../../../public/images/audio_play2.png)
![image of commandline](../../../../public/images/audio_play3.png)

-----


## Ideas

It's interesting where this could lead to, could a more powerful javascript player be used?
Or could this even be used for a basic transcription service with a text field to correct or save the annotation?

____

## Links
- [Link](https://github.com/robmsmt/SpeechLoop/blob/master/notebooks/play_audio_in_df.ipynb) to the full jupyter notebook code

