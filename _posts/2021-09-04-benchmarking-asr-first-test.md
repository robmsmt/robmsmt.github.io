---
layout: post
title: Benchmarking ASRs - Results from the first 'Out of the Box' Test
---

First 'Out of the box' results for 5 ASRs: VOSK vs Sphinx vs Coqui vs Google vs Amazon


____
<link rel="stylesheet" href="{{ site.baseurl }}{{ '/public/css/mermaid.min.css'}}">
<script src='{{ site.baseurl }}/public/js/gridjs.umd.js'></script>

## 1. Overview

In this post I am going to go through the testing procedure on the initial small dataset that is included with
SpeechLoop. Speech systems are updated continuously so it's sometimes hard to keep track of what the latest WER of each
system is. This tool allows for easy benchmarking to answer that question.

Although the test itself has a few limitations, it's very small and the simplest which may be useful for those who want
to quickly get up and running and evaluating different speech systems.

____


## 2. The Data

There are 78 WAVs of a single female person speaking very short nouns (mostly single words).

```text
    FileCount: 78
    TotalLength: 00:01:15
    ReadType: read
    AudioQualityType: high
    AudioNoiseType: none
    MicQuality: high
    AudioDist: near-field
    Persons: solo
    Language: EN
    Accent: US
    Gender: female
    Filetype: wav
    AudioFile: Signed 16 bit Little Endian, Rate 16000 Hz, Mono
```

Although this isn't a great comprehensive test on it's own - it is another single data point that can help paint the
picture of how good the ASR is in real world settings. The details on the dataset can be
found [here](https://github.com/robmsmt/SpeechLoop/tree/master/speechloop/data/simple_test).

<div class="message">
  🎉 The dataset is open sourced under creative commons along with the <a href="https://github.com/robmsmt/SpeechLoop">SpeechLoop tool</a> under Apache2 for anyone to download and repeat.
I would appreciate anyone who validates and double checks and finds flaws in any part of the data/test.
</div>

Sometimes single word commands are harder for ASRs when using WER as the metric. This is for 2 reasons, firstly, because the utterances are usually very short
there's less information to evaluate (relies on the acoustic matching more so then pure unigram probabilities). Secondly, a single character when wrong can cause the word to be incorrect which contributes very large
error to the overall score. This can make this a useful test to run!

____


## 3. Setup

Since this is the first benchmark I will repeat every step in detail so that others can follow.

Since we are planning to use GoogleASR and AWSTranscribe we need to setup the credentials. My prefered way is in the ~
/.bashrc:

```shell
#AWS
export AWS_ACCESS_KEY_ID=<accesskeyhere>
export AWS_SECRET_ACCESS_KEY=<secret_access_key_here>
#GOOGLE
export GOOGLE_APPLICATION_CREDENTIALS=/home/you/path/to/your/creds.json
```

You can then re-source the bashrc with: `source ~/.bashrc`

Next, let's setup the repo as per the [instructions](https://github.com/robmsmt/SpeechLoop#developer---2-step-install):

```shell
git clone https://github.com/robmsmt/SpeechLoop && cd SpeechLoop
python3 -m venv venv/py3
source venv/py3/bin/activate
pip install -r requirements-dev.txt
```

Then to run:

```shell
cd speechloop
python main.py --input_csv='data/simple_test/simple_test.csv' --wanted_asr=all
```

____


## 4. ASRs

When we run the script we provide `--wanted_asr=all` this means (as of writing):

- sp = Sphinx - pocketsphinx 0.1.15
- vs = Vosk - based off of model en-small-us-0.15
- cq = Coqui - based off of model v0.9.3
- gg = GoogleASR - 21-09-06
- aw = AwsTranscribe - 21-09-06

The last two being cloud ASRs so all we can use to distinguish is today's date.

____


## 5. Results

After some minor tidying up of the dataframe, see
the [Jupyter notebook](https://github.com/robmsmt/SpeechLoop/blob/master/notebooks/21-09-04-Benchmarking-ASR-first-test.ipynb)
for details... we finally get a dataframe which we can export to JSON, so you can see below.

  <div id="wrapper"></div>
  <script type="text/javascript">
    new gridjs.Grid({
      columns: ["filename","vs","sp","cq","gg","aw","transcript","vs_wer","sp_wer","cq_wer","gg_wer","aw_wer", "comb_wer"],
      sort: true,
      pagination: {
        enabled: true,
        limit: 20,
        summary: true
      },
      resizable: false,
      search: true,
      autoWidth: true,
      width: "150%",
      style: {
        td: {
          'padding-left': '1px',
          'padding-right': '1px'
        },
        th: {
          'padding-left': '1px',
          'padding-right': '1px'
        },
        table: {
          'font-size': '14px',
          'margin': '2px'
        },
        gridjs:{
          "width": "150%"
        }
      },
      data: [["109865_bear_ch0_16k.wav","bear","they are","beare","bear","There","bear",0.0,1.0,1.0,0.0,1.0,3.0],["109879_frog_ch0_16k.wav","frog","for ah ok ","forog","frog","Frog","frog",0.0,1.0,1.0,0.0,0.0,2.0],["109886_ice-cream_ch0_16k.wav","ice cream","ice cream","i cream","ice cream","Scream","ice cream",0.0,0.0,0.5,0.0,1.0,1.5],["109881_glass_ch0_16k.wav","glass","glass","glass","glass","Glass","glass",0.0,0.0,0.0,0.0,0.0,0.0],["109868_camera_ch0_16k.wav","camera","camera","camera","camera","Camera","camera",0.0,0.0,0.0,0.0,0.0,0.0],["109869_car_ch0_16k.wav","car","hello ","car","car","Car","car",0.0,1.0,0.0,0.0,0.0,1.0],["109902_nail_ch0_16k.wav","nail","nail ","nail","nail","Nail","nail",0.0,0.0,0.0,0.0,0.0,0.0],["109885_horse_ch0_16k.wav","horst","horses","horse","horse","Horse","horse",1.0,1.0,0.0,0.0,0.0,2.0],["109920_tiger_ch0_16k.wav","tiger","hiking","tiger","tiger","Tiger","tiger",0.0,1.0,0.0,0.0,0.0,1.0],["109874_earth_ch0_16k.wav","earth","earth","h","Earth","Earth","earth",0.0,0.0,1.0,0.0,0.0,1.0],["109864_apple_ch0_16k.wav","apple","at home","aple","Apple","Apple","apple",0.0,1.0,1.0,0.0,0.0,2.0],["109917_skateboard_ch0_16k.wav","skateboard","skateboard","skateboard","skateboard","Skateboard","skateboard",0.0,0.0,0.0,0.0,0.0,0.0],["109931_windows_ch0_16k.wav","windows","windows","windows","windows","Windows","windows",0.0,0.0,0.0,0.0,0.0,0.0],["109924_uniform_ch0_16k.wav","uniform","you know for him","youniform","uniform","Uniform","uniform",0.0,1.0,1.0,0.0,0.0,2.0],["109928_volcano_ch0_16k.wav","for kaino","volcano","full cano","volcano","Volcano","volcano",1.0,0.0,1.0,0.0,0.0,2.0],["109911_quartz_ch0_16k.wav","quartz","quartz","cuorts","forts","Quartz","quartz",0.0,0.0,1.0,1.0,0.0,2.0],["109916_rhinoceros_ch0_16k.wav","rhinoceros","rhinoceros","ri noseris","rhinoceros","Rhinoceros","rhinoceros",0.0,0.0,1.0,0.0,0.0,1.0],["109935_yacht_ch0_16k.wav","the ot","yeah  its","heoght","yacht","Yacht","yacht",1.0,1.0,1.0,0.0,0.0,3.0],["109883_hammer_ch0_16k.wav","hammer","hammer","hammer","hammer","Hammer","hammer",0.0,0.0,0.0,0.0,0.0,0.0],["109900_mouse_ch0_16k.wav","mouse","malice","nows","Mouse","Mouse","mouse",0.0,1.0,1.0,0.0,0.0,2.0],["109904_nutshell_ch0_16k.wav","nut shell","next show ","not shell","nutshell","Nutshell","nutshell",1.0,1.0,1.0,0.0,0.0,3.0],["109913_question-mark_ch0_16k.wav","question mark","question mark","question mark","question mark","Question Mark","question mark",0.0,0.0,0.0,0.0,0.0,0.0],["109922_train_ch0_16k.wav","train","train","train","train","Train","train",0.0,0.0,0.0,0.0,0.0,0.0],["109932_xebec_ch0_16k.wav","z back","citibank","zeback","the back","Z back","xebec",1.0,1.0,1.0,1.0,1.0,5.0],["109919_star_ch0_16k.wav","star","star","star","star","Star","star",0.0,0.0,0.0,0.0,0.0,0.0],["109870_cow_ch0_16k.wav","cow","town","cow","cow","How","cow",0.0,1.0,0.0,0.0,1.0,2.0],["109896_leaf_ch0_16k.wav","leaf","leaf ","leaf","leaf","Leave","leaf",0.0,0.0,0.0,0.0,1.0,1.0],["109899_mars_ch0_16k.wav","mars","maurice","mars","Mars","Mars","mars",0.0,1.0,0.0,0.0,0.0,1.0],["109906_orange_ch0_16k.wav","orange","orange","ornge","Orange","Orange","orange",0.0,0.0,1.0,0.0,0.0,1.0],["109923_umbrella_ch0_16k.wav","umbrella","umbrella","umbrella","umbrella","Umbrella","umbrella",0.0,0.0,0.0,0.0,0.0,0.0],["109912_queen_ch0_16k.wav","queen","queen","queen","Queen","Queen","queen",0.0,0.0,0.0,0.0,0.0,0.0],["109929_waffle_ch0_16k.wav","waffle","waffle","waffl","waffle","Waffle","waffle",0.0,0.0,1.0,0.0,0.0,1.0],["109867_butterfly_ch0_16k.wav","butterfly","butterfly","terfly","butterfly","Butterfly","butterfly",0.0,0.0,1.0,0.0,0.0,1.0],["109937_yarn_ch0_16k.wav","yarn","yeah lines","yan","yarn","Joan","yarn",0.0,1.0,1.0,0.0,1.0,3.0],["109898_lion_ch0_16k.wav","lion","lie in","lion","lion","Lion","lion",0.0,1.0,0.0,0.0,0.0,1.0],["109905_oars_ch0_16k.wav","worse","lawyers","or","ORS","Wars","oars",1.0,1.0,1.0,1.0,1.0,5.0],["109925_union_ch0_16k.wav","union","union","yunion","Union","Union","union",0.0,0.0,1.0,0.0,0.0,1.0],["109877_fire_ch0_16k.wav","fire","fire","fire","fire","Fire","fire",0.0,0.0,0.0,0.0,0.0,0.0],["109927_violin_ch0_16k.wav","violin","wyoming","sioin","violin","Violin","violin",0.0,1.0,1.0,0.0,0.0,2.0],["109918_spoon_ch0_16k.wav","spoon","spoon","spoon","spoon","Spoon","spoon",0.0,0.0,0.0,0.0,0.0,0.0],["109894_key_ch0_16k.wav","key","key","key","key","Key","key",0.0,0.0,0.0,0.0,0.0,0.0],["109863_ant_ch0_16k.wav","yet","and","ant","aunt","Yet","ant",1.0,1.0,0.0,1.0,1.0,4.0],["109884_helicopter_ch0_16k.wav","helicopter","helicopter","helocopter","helicopter","Helicopter","helicopter",0.0,0.0,1.0,0.0,0.0,1.0],["109933_xiphias_ch0_16k.wav","z fierce","fifty s","sif hes","xiphias","Ziff Ius","xiphias",1.0,1.0,1.0,0.0,1.0,4.0],["109888_iron_ch0_16k.wav","iron","i am and","arn","iron","Iron","iron",0.0,1.0,1.0,0.0,0.0,2.0],["109936_yak_ch0_16k.wav","yak","yeah","yeh","yak","Jack","yak",0.0,1.0,1.0,0.0,1.0,3.0],["109897_lemon_ch0_16k.wav","men","lenin","lemon","lemon","Lemon","lemon",1.0,1.0,0.0,0.0,0.0,2.0],["109875_egg_ch0_16k.wav","a good","a p","a","egg","Big","egg",1.0,1.0,1.0,0.0,1.0,4.0],["109921_tomato_ch0_16k.wav","tomato","tomato ","to mateo","tomato","Tomato","tomato",0.0,0.0,1.0,0.0,0.0,1.0],["109907_owl_ch0_16k.wav","our","now on","ou","owl","How will","owl",1.0,1.0,1.0,0.0,1.0,4.0],["109873_duck_ch0_16k.wav","duck","doc",null,"duck","Duck","duck",0.0,1.0,1.0,0.0,0.0,2.0],["109862_airplane_ch0_16k.wav","airplane","airplane","plane","airplane","Airplane","airplane",0.0,0.0,1.0,0.0,0.0,1.0],["109940_zucchini_ch0_16k.wav","zucchini","zucchini","sux keny","zucchini","Zucchini","zucchini",0.0,0.0,1.0,0.0,0.0,1.0],["109889_jacket_ch0_16k.wav","jacket","jackets","jacket","jackets","Jacket","jacket",0.0,1.0,0.0,1.0,0.0,2.0],["109880_galaxy_ch0_16k.wav","galaxy","galaxy",null,"Galaxy","Galaxy","galaxy",0.0,0.0,1.0,0.0,0.0,1.0],["109910_pig_ch0_16k.wav","peak","panic","pick","Pig","Take","pig",1.0,1.0,1.0,0.0,1.0,4.0],["109882_glove_ch0_16k.wav","love","i love ","love","glove","Love","glove",1.0,1.0,1.0,0.0,1.0,4.0],["109930_wallet_ch0_16k.wav","wallet","was it","wallet","wallet","Wallet","wallet",0.0,1.0,0.0,0.0,0.0,1.0],["109915_rain_ch0_16k.wav","mean","maine","rain","rain","Rain","rain",1.0,1.0,0.0,0.0,0.0,2.0],["109938_zebra_ch0_16k.wav","zebra","zebra","zebra","zebra","Zebra","zebra",0.0,0.0,0.0,0.0,0.0,0.0],["109893_kayak_ch0_16k.wav","kayak","kayak","kayk","kayak","Kayak","kayak",0.0,0.0,1.0,0.0,0.0,1.0],["109887_igloo_ch0_16k.wav","the glue","a clue","lue","igloo","Igloo","igloo",1.0,1.0,1.0,0.0,0.0,3.0],["109926_video-camera_ch0_16k.wav","video camera","video camera","fideo camera","video camera","Video camera","video camera",0.0,0.0,0.5,0.0,0.0,0.5],["109939_zero2_ch0_16k.wav","zero","zero","zero","zero","Zero","zero",0.0,0.0,0.0,0.0,0.0,0.0],["109914_rabbit_ch0_16k.wav","rabbit","maggots","rabbit","rabbit","Rabbit","rabbit",0.0,1.0,0.0,0.0,0.0,1.0],["109866_boat_ch0_16k.wav","boat","boat","o","boats","Vote","boat",0.0,0.0,1.0,1.0,1.0,3.0],["109872_dog_ch0_16k.wav","dog","dog","dog","dog","Dog","dog",0.0,0.0,0.0,0.0,0.0,0.0],["109934_xylophone_ch0_16k.wav","xylophone","silent ","zi o phone","xylophone","Xylophone","xylophone",0.0,1.0,1.0,0.0,0.0,2.0],["109909_pen_ch0_16k.wav","penn","palin","pen","pain","Pen","pen",1.0,1.0,0.0,1.0,0.0,3.0],["109895_king_ch0_16k.wav","king","king","king","King","King","king",0.0,0.0,0.0,0.0,0.0,0.0],["109876_elephant_ch0_16k.wav","elephant","elephants","ant","elephant","Elephant","elephant",0.0,1.0,1.0,0.0,0.0,2.0],["109892_juice_ch0_16k.wav","juice","she reads the","hoose","shoes","Juice","juice",0.0,1.0,1.0,1.0,0.0,3.0],["109871_daisy_ch0_16k.wav","daisy","daisy","y","Daisy","Easy","daisy",0.0,0.0,1.0,0.0,1.0,2.0],["109903_neptune_ch0_16k.wav","neptune","neptune","neptune","Neptune","Neptune","neptune",0.0,0.0,0.0,0.0,0.0,0.0],["109878_fork_ch0_16k.wav","for","fork","for","Fork","Fork","fork",1.0,0.0,1.0,0.0,0.0,2.0],["109890_jaguar_ch0_16k.wav","jag you are","jake you are","jakyu are","Jaguar","Shake. You are","jaguar",1.0,1.0,1.0,0.0,1.0,4.0],["109901_mushroom_ch0_16k.wav","mushroom","mushroom","mushroom","mushroom","Mushroom","mushroom",0.0,0.0,0.0,0.0,0.0,0.0],["109908_paint_ch0_16k.wav","paint","paint","paint","paint","Paint","paint",0.0,0.0,0.0,0.0,0.0,0.0]]
    }).render(document.getElementById("wrapper"));
  </script>

Something interesting to note, the WAVs "xebec" and "oars" all ASRs got wrong.

There were many words that ALL ASRs got correct e.g. "class", "camera", "nail", "skateboard", "windows" and "hammer".

____


## 6. Conclusion

In summary once we have the corrections of each ASR in place the results are.

  <img class="img-fluid" src="../../../../public/images/210904_benchmark_wer.png" alt="wer" width="650">

  <div id="wrapper2"></div>
  <script type="text/javascript">
    new gridjs.Grid({
      columns: ["ASR", "Type", "Mean WER"],
      sort: true,
      pagination: false,
      data: [
        ["Vosk (vs)", "Offline - Docker", "0.23"],
        ["Sphinx (sp)", "Offline - Docker", "0.47"],
        ["Coqui (cq)", "Offline - Docker", "0.53"],
        ["GoogleASR (gg)", "API", "0.10"],
        ["AWSTranscribe (aw)", "API", "0.21"]
      ]
    }).render(document.getElementById("wrapper2"));
  </script>

<br>
Overall the results are somewhat expected. CloudASRs tend to dominate, with VOSK being very good modern alternative. Coqui had
surprisingly bad results but we'd expected it to do better on longer sentences or with a customized language model.

For the next tests planned, we will attempt to tune each for the above dataset (where possible). As well as
trying on everyone's favourite opensource dataset...!
