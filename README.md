# IVA

## Components

* [Iva](https://github.com/project-iva/iva) is the core component, which orchestrates all the other components. It
  handles incoming events and performs relevant actions or schedules relevant (timed)
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
* [iOS + watchOS extension](https://github.com/project-iva/iva_ios) leverages healtkit to retrieve readings from
  different apps (sleep, mindfulness, weight, etc.) and forwards them to the backend. Thanks to background delivery new
  data can be presented in the frontend as soon as they are recorded. Additionally, it lets users create day
  planning/todos and update them. Lastly, it can be used to control the presenter device and to handle voice commands (
  currently done by using Apple's speech to text/dictation capabilities and by sending the recognized text to Iva for
  intent classification). Comes with watch extension for convenience.
* [intent classifier server](https://github.com/project-iva/iva_bert_classifier_api) servers fine-tuned distilBERT for
  intent classification.
* [traefik](https://github.com/project-iva/iva_traefik) used for easy networking from the outside of the docker network.
