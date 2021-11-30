# IVA

Event-driven (to be) virtual assistant and smart home management system. The intention is to help with planning/time
management, automate routines, aggregating data from multiple sources and provide insides.

## Components

* [Iva](https://github.com/project-iva/iva) is the core component, which orchestrates all the other components. It acts
  as event manager, handling incoming events and performing relevant actions or scheduling relevant (timed)
  events.

  For example:
    * incoming utterance from the app or from the slack channel -> classify the utterance and schedule a new event which
      handles the intent
    * new data available in the backend -> notify the frontend about which components should be refreshed
    * it's 7:30AM and a morning routine was scheduled for this time -> turn on the screen and start a routine
* [backend](https://github.com/project-iva/iva_backend) handles persistent storage of data (healthkit data, utterances
  and corresponding intents, day planning/todos, etc.). It aggregates data from different sources, which in the future
  should enable for deeper analysis. Furthermore, it handles pre-processing of the available data into easily consumable
  weekly overviews and recurring tasks, such as fetching latest asset(stocks/crypto) values for portfolio tracking
  purposes.
* [frontend](https://github.com/project-iva/iva_frontend) presents weekly overview stats, portfolio, day planning/todos,
  etc. Furthermore, it acts as a presenter for routines. In the future, it should also be used to create long term
  planning and for easy data exploration.
* [raspberry-client](https://github.com/project-iva/iva_raspberry_client) is used to control the device on which the
  presenter (frontend) is running.
* [iOS app + watchOS extension](https://github.com/project-iva/iva_ios) leverages healtkit to retrieve readings from
  different apps (sleep, mindfulness, weight, etc.) and forwards them to the backend. Thanks to background delivery new
  data can be presented in the frontend as soon as they are recorded. Additionally, it lets users create day
  planning/todos and update them. Lastly, it can be used to control the presenter device and to handle voice commands.
  Comes with watch extension for convenience.
* [intent classifier server](https://github.com/project-iva/iva_bert_classifier_api) servers fine-tuned distilBERT for
  intent classification.
* [traefik](https://github.com/project-iva/iva_traefik) used for easy networking from the outside of the docker network.

## Intent classification and training data collection

Instead of manually creating a dataset and labeling it, I opted for a more interesting approach by mixing
semi-supervised learning and active learning. I started by creating 2 samples per intent, fine-tuned and deployed the
model. When an utterance is provided and the result is intent with low confidence, then the user is promoted to confirm
the correctness of the classification. The user is able to confirm that the classification is correct/chose the correct
intent or create an entirely new intent. These utterances together with their labels are saved and used to re-train the
model. Afterwards, this process is repeated.

There are some issues with this approach. Namely, almost identical utterances will keep getting repeated, not providing
any new information. And potentially even inhibiting the performance by skewing the label distribution of the dataset.

The balance will always need to be considered, however, there are some possible mitigations to avoid almost identical (
non-informative) utterances:

1. Only save utterances when the confidence is bellow certain threshold, which could mean that this particular utterance
   is informative.
2. Clean up the dataset before training by removing non-informative samples, to find them we can:
    1. Use model such as DistilBERT to embed the entire utterance in the vector space and calculate cosine similarity.
    2. Simpler approach would be to use some kind of metric to calculate the distance between pair. Metrics used in
       machine translation such as BLEU can be considered. However, this approach probably won't yield good results,
       since most of these metrics are based around comparing uni/bi/tri...-grams, while utterances can differ in only
       single word and have completely opposite meaning e.g. "turn the screen on/off"

### Intents

As of now the system can recognize the following intents:

* TURN_SCREEN_ON
* TURN_SCREEN_OFF
* INTRODUCE_YOURSELF
* TELL_TIME
* SPOTIFY_PLAY
* SPOTIFY_STOP
* TELL_JOKE
* START_MORNING_ROUTINE
* START_EVENING_ROUTINE

## Speech-to-text and text-to-speech

Speech-to-text is currently handled by Apple's speech module(iOS) and dictation (watchOS). And text-to-speech is done
via Google's TTS API. If time and other resources allow, it would be very interesting to build a custom system.

## Demo
### Handling utterances from a chat app such as Slack
https://user-images.githubusercontent.com/10901373/143785844-3ccd1667-84b4-4888-89e3-8a6aaae7eade.mp4

### Handling unknown intents
https://user-images.githubusercontent.com/10901373/143785855-8e74f8b6-3775-4d8c-9de9-8195b9a3d5d8.mp4


### Frontend/Presenter
![presenter](https://user-images.githubusercontent.com/10901373/143780844-4940be8b-64d4-427c-a5f2-14fdf04314af.png)


### Day planning/todos
https://user-images.githubusercontent.com/10901373/143782791-d0b9abf5-12df-499f-9fab-02d00a55cd1b.mp4


### Handle utterance
https://user-images.githubusercontent.com/10901373/143782349-ea1ba3d3-7314-4868-a1d2-d0e16e8b1758.mov


### Control session/routine with your watch
https://user-images.githubusercontent.com/10901373/143782361-6aee4deb-8391-4e8e-a26f-2caab444eb11.mov



