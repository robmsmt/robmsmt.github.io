---
layout: post
title: Introducing SpeechLoop
---

I made a [tool](github.com/robmsmt/SpeechLoop) to evaluate multiple ASR (automatic speech recognition) engines and benchmark them to help answer the question, what is the best ASR?


## The Problem(s)

Every time you find an ASR, commercial or otherwise, they often say or imply that they are the best.

<img class="img-fluid" src="../../../../public/images/best_diner.jpg" alt="best-diner" width="700">
It's a very difficult claim to substantiate. How do they know they're the best? Maybe they've publicly benchmarked? Sometimes you'll get a vague chart with cherry picked results.
Other times you'll get a more detailed study. But still it's hard to believe that the person doing the benchmarking is likely to report findings that show their ASR in a bad light.

Progress in the ML space moves very quickly. Overnight a new model is trained.
What about the papers last year with the new dizzying end-2-end-2-end with mega-transformers? and with guest appearances from all your favourite childhood tv-shows. Were these compared?
It can be difficult to keep track, plus you don't want to keep hitting your competitors' ASR all the time - this takes time away from R&D.

>    <i>One can judge from experiment, or one can blindly accept authority.</i>
<br>Robert A. Heinlein

This is one of the reasons why SpeechLoop was born.

____

## Design Decisions
The following were some design decisions I made:
 - **Easy to use with sensible defaults** - following the "for humans" philosophy that other python packages have popularized.
 - **Heavy use of docker where required** - Docker completely solves the issue of having to install big and heavily conflicting libraries, some of which are only needed for the
install part of the process. If you have a reasonable amount of disk space and any x86_64 cpu you can go quite far with just sending bytes to an already built container.
 - **Limited number of 3rd party dependencies** - this can be a challenge when installing a library, the more packages the more that can go wrong.
 - **Pandas Dataframes** - for easy analysis and simple WER calculation... the dataframe felt like the best fit where each row corresponds to an audio file and each ASR transcript would be a column.

____

## Features
There are many features to list, but I think a video of simple usage will be even more useful.

<img class="img-fluid" src="../../../../public/images/cli_fast.gif" alt="cli" width="700">
Here we demonstrate using a the CLI to record microphone and pipe the data into the selected 3 ASRs.

<video  style="display:block; width:700px; height:auto;" autoplay controls loop="loop">
           <source src="../../../../public/images/cli_testwavs.webm"  type="video/webm"  />
 </video>
Above we run the CLI to evaluate against the included test dataset. It outputs a CSV.

```text
------------------------------
Average value of vs_corrected_wer is 0.2308

------------------------------
Average value of sp_corrected_wer is 0.5128

------------------------------
Average value of cq_corrected_wer is 0.5513

Total inference time taken for vs is 0:01:15.081824
Total inference time taken for sp is 0:00:42.342496
Total inference time taken for cq is 0:01:27.646377
------------------------------
------------------------------
Finished, with total runtime: 0:03:42
```

<img class="img-fluid" src="../../../../public/images/calc.png" alt="cli" width="700">
Often it's good to load the CSV in excel-like tools to view the result.

____

## Future
 - Different data formats (e.g. Kaldi has SCP, it would be nice to read those)
 - Cost calculator for APIs
 - More ASRs
 - Benchmarks of common datasets - for example librispeech or one of the [newly released huge datasets](https://github.com/robmsmt/ASR-Audio-Data-Links/#1-free)

____

## Thanks
- Thanks to all ASRs - this tool just builds on top of others work. Without them it wouldn't make sense.
- My employer [MerlynMind](https://www.merlyn.org) who have actively encouraged this work - note that we are currently hiring for many roles.

