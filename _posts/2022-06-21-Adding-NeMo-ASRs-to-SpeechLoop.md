---
layout: post
title: Adding NeMo ASRs to SpeechLoop
---

This is a short blog post about adding NVIDIA's NeMo ASR toolkit to the SpeechLoop pipeline to help us keep track of the latest developments in the speech recognition field.


____
<link rel="stylesheet" href="{{ site.baseurl }}{{ '/public/css/mermaid.min.css'}}">
<script src='{{ site.baseurl }}/public/js/gridjs.umd.js'></script>

## NVIDIA NeMo Overview

<img class="img-fluid" src="../../../../public/images/nemo.png" alt="nemo" width="700">


In 2019 NVIDIA released a toolkit which has helped the Speech recognition field as it has raised the bar on the quality of pretrained models that are available for researchers and production engineers alike to easily test under a single toolkit. Toolkits which make heavy use of their expensive GPUs make good strategic sense and encourage the use of their propreietary CUDA libraries. However, the toolkit as well as the models themselves are released with a very permissive open source "[Apache License 2.0](https://github.com/NVIDIA/NeMo/blob/main/LICENSE)" licence which is friendly for commercial use as well as not being overly restrictive. 

Since the initial commit, the github repo has had over 4k commits and is one of the more active speech projects on github showcasing a number of different recent developments for example, Conformer, Conformer-Transducer, LSTM-Transducer, LSTM-CTC. Each of these, in their own right, could warrant their own blog post. NeMo has since expanded beyond just speech transcription and with Voice activity Detection (VAD), Speaker Recognition, Speaker Diarization, different languages as well as some NLP tasks. 

Whilst the toolkit itself is full featured, there are a large number of dependencies (over 150 required for just ASR install) which can make setting it up just for an initial probe into the "out of the box" inference only performance a hinderance.


## What We Did

SpeechLoop works by abstracting out a lot of the libraries and dependencies through docker containers. This means that the core SpeechLoop application only has a handful of small dependencies which are usually quick and painless to install. The docker container has all the dependencies embedded inside so we are able to build this and upload to dockerhub for anyone to download when NeMo is selected at SpeechLoop runtime.

We evaluated all of the NeMo ASRs by downloading them all and then testing them, we specifically were looking at the size of the models since we were not planning on using any models that are too big since they are more expensive to run and have a lot of challenges running on smaller footprint hardware. 

```python
import nemo
import nemo.collections.asr as nemo_asr

# the nemo models we will look at are english - EncDecCTCModels, EncDecCTCModelBPE, EncDecRNNTBPEModel, EncDecRNNTModel

enc_dec_ctc_models = [(x.pretrained_model_name, nemo_asr.models.EncDecCTCModel.from_pretrained(model_name=x.pretrained_model_name)) for x in nemo_asr.models.EncDecCTCModel.list_available_models() if "en" in x.pretrained_model_name]
enc_dec_ctc_bpe_models = [(x.pretrained_model_name, nemo_asr.models.EncDecCTCModelBPE.from_pretrained(model_name=x.pretrained_model_name)) for x in nemo_asr.models.EncDecCTCModelBPE.list_available_models() if "en" in x.pretrained_model_name]
enc_dec_rnn_t_bpe_models = [(x.pretrained_model_name, nemo_asr.models.EncDecRNNTBPEModel.from_pretrained(model_name=x.pretrained_model_name)) for x in nemo_asr.models.EncDecRNNTBPEModel.list_available_models() if "en" in x.pretrained_model_name]
enc_dec_rnn_t_models = [(x.pretrained_model_name, nemo_asr.models.EncDecRNNTModel.from_pretrained(model_name=x.pretrained_model_name)) for x in nemo_asr.models.EncDecRNNTModel.list_available_models() if "en" in x.pretrained_model_name]

all_models = enc_dec_ctc_models + enc_dec_ctc_bpe_models + enc_dec_rnn_t_bpe_models + enc_dec_rnn_t_models

for model in all_models:
    print(model.name)
    print(model)
    model.save_to(model.name+'.nemo')
```

Now we have the checkpoint models for all 22 different NeMo engines and can compare their sizes as well as results. See table of initial results bellow.

!insert table!111



## Analysis


After analysis of the data we were able to conclude that the `stt_en_contextnet_1024` model looked the most promising since it was under 1GB in size and had the 2nd best performance for the dataset we had. This meant that we used the model as our first alpha model (before adding the remaining models).


!Add Comments here!111


Since the NeMo model is in docker hub you can now pull it and download with:
`docker pull robmsmt/sl-nemo-en-16k`


## Comparing to other ASRs through SpeechLoop


Part of the power of using SpeechLoop is that it easily allows us to take the new ASR and using an existing dataset and compare the results with already added engines, now that the NeMo container is ready we run this NeMo model on our default dataset mentioned [here](https://robmsmt.github.io/2021/09/04/benchmarking-asr-first-test/).


  <div id="wrapper2"></div>
  <script type="text/javascript">
    new gridjs.Grid({
      columns: ["ASR", "Type", "Mean WER"],
      sort: true,
      pagination: false,
      data: [
        ["Vosk (vs)", "Offline - Docker", "0.2308"],
        ["Sphinx (sp)", "Offline - Docker", "0.4936"],
        ["Coqui (cq)", "Offline - Docker", "0.5513"],
        ["NeMo (nm)", "Offline - Docker", "0.0897"],
        ["GoogleASR (gg)", "API", "0.1154"]
      ]
    }).render(document.getElementById("wrapper2"));
  </script>


## Next Steps


 - We would like to reduce the model size from ~4GB to around 500mb plus the model by using minimal libraries in the container, the Onnx - Microsoft's Onnx Runtime library allows this and is probably the best option.


 - Add remaining models - by enumerating each lib we can simply provide all the major models from NeMo through a very simple naming structure.


