# Configuration

To confiure your NSPanel to your needs, you need to edit the `apps.yaml` inside of your Appdaemon config folder and add card and entities you want to display on the screen.

If you've sucessfully set up mqtt, you should already have a configuration looking like this:

```yaml
---
nspanel-1:
  module: nspanel-lovelace-ui
  class: NsPanelLovelaceUIManager
  config:
    panelRecvTopic: "tele/tasmota_your_mqtt_topic/RESULT"
    panelSendTopic: "cmnd/tasmota_your_mqtt_topic/CustomSend"
```

You can continue from this point adding configuration for the weather forcecast on the screensaver, configuring a schedule for the brightness of the screensaver and your first cards.

```yaml
---
nspanel-1:
  module: nspanel-lovelace-ui
  class: NsPanelLovelaceUIManager
  config:
    panelRecvTopic: "tele/tasmota_your_mqtt_topic/RESULT"
    panelSendTopic: "cmnd/tasmota_your_mqtt_topic/CustomSend"
    sleepTimeout: 20
    sleepBrightness:
      - time: "7:00:00"
        value: 10
      - time: "23:00:00"
        value: 0
    locale: "de_DE"
    screensaver:
      entity: weather.k3ll3r
    cards:
      - type: cardEntities
        entities:
          - entity: switch.example_item
          - entity: light.example_item
        title: Example 1
      - type: cardGrid
        entities:
          - entity: switch.example_item
          - entity: light.example_item
        title: Example 2
```

This is the full list of configuration options supported for the config key:

key | optional | type | default | description
-- | -- | -- | -- | --
`panelRecvTopic` | False | string | `tele/tasmota_your_mqtt_topic/RESULT` | The mqtt topic used to receive messages. 
`panelSendTopic` | False | string | `cmnd/tasmota_your_mqtt_topic/CustomSend` | The mqtt topic used to send messages. 
`updateMode` | True | string | `auto-notify` | Update Mode; Possible values: "auto", "auto-notify", "manual"
`model` | True | string | `eu` | Model; Possible values: "eu", "us-l" and "us-p"
`sleepTimeout` | True | integer | `20` | Timeout for the screen to enter screensaver, to disable screensaver use 0
`sleepBrightness` | True | integer/complex | `20` | Brightness for the screen on the screensaver, see example below for complex/scheduled config.
`screenBrightness` | True | integer/complex | `100` | Brightness for the screen during usage, config format is the same as sleepBrightness.
`sleepTracking` | True | string | None | Forces screensaver brightness to 0 in case entity state is not_home or off, can be a group, person or device_tracker entity.
`sleepOverride` | True | complex | None | Allows overriding of the sleepBrightness if entity state is on, true or home. Overrides sleepBrightness but sleepTracking takes precedence.
`locale` | True | string | `en_US` | Used by babel to determinante Date format on screensaver, also used for localization.
`dateFormatBabel` | True | string | `full` | formatting options on https://babel.pocoo.org/en/latest/dates.html?highlight=name%20of%20day#date-fields
`timeFormat` | True | string | `%H:%M` | Time Format on screensaver. Substring after `?` is displayed in a seperate smaller textbox. Useful for 12h time format with AM/PM  <pre>`"%I:%M   ?%p"`</pre>
`dateAdditionalTemplate` | True | string | `""` | Addional Text dispayed after Date, can contain a Homeassistant Template Example `" - {{ states('sun.sun') }}"`
`timeAdditionalTemplate` | True | string | `""` | Addional Text dispayed below Time, can contain a Homeassistant Template
`dateFormat` | True | string | `%A, %d. %B %Y` | date format used if babel is not installed
`cards` | False | complex | | configuration for cards that are displayed on panel; see docs for cards
`screensaver` | True | complex | | configuration for screensaver; see docs for screensaver
`hiddenCards` | True | complex | | configuration for cards that can be accessed though navigate items; see docs for cards

<details>
<summary>Details on sleepBrightness/screenBrightness and other configs related to screen brightness</summary>
<br>

It is possible to schedule a brightness change for the screen at specific times.

```yaml
    sleepBrightness:
      - time: "7:00:00"
        value: 10
      - time: "23:00:00"
        value: 0
```

```yaml
    sleepBrightness:
      - time: "sunrise"
        value: 10
      - time: "sunset + 1:00:00"
        value: 0
```

It is also possible to use a static value or an input_number/sensor with the range between 0 and 100 as value for sleepBrightness/screenBrightness:

```
    sleepBrightness: input_number.brightness_nspanel
```

```
    sleepBrightness: 50
```


`sleepTracking` overrides this setting and sets the brightness to 0 if the state of the configured Home Assistant entity is `off` or `not_home`. You may also use a [Home Assistant group](https://www.home-assistant.io/integrations/group) to track multiple entities.

`sleepOverride` overrides sleepBrightness but does not take precedence over sleepTracking. This is useful if, for example, you want your NSPanel to be brighter than usual if your light is on or if you want to override a panel dimming if you are in the room.

The following example configuration is turning off the screen after sunset, but in case the bedroom light is on the NSPanel brightness will be 20 instead of 0.

```yaml
    sleepBrightness:
      - time: "sunrise"
        value: 20
      - time: "sunset"
        value: 0
    sleepOverride:
      entity: light.bedroomlight
      brightness: 20
```


</details>

<details>
<summary>Supported keys for locale config</summary>
<p>

  - `af_xx` - Afrikaans
  - `ar_xx` - Arabic
  - `bg_xx` - Bulgarian
  - `ca_xx` - Catalan
  - `cs_xx` - Czech
  - `da_xx` - Danish
  - `de_xx` - German
  - `el_xx` - Greek
  - `en_xx` - English
  - `es_xx` - Spanish
  - `et_xx` - Estonian
  - `fa_xx` - Persian
  - `fi_xx` - Finnish
  - `fr_xx` - French
  - `he_xx` - Hebrew
  - `hr_xx` - Croatian
  - `hu_xx` - Hungarian
  - `hy_xx` - Armenian
  - `id_xx` - Indonesian
  - `is_xx` - Icelandic
  - `it_xx` - Italian
  - `lb_xx` - Luxembourgish
  - `lt_xx` - Lithuanian
  - `lv_xx` - Latvian
  - `nb_xx` - Norwegian
  - `nl_xx` - Dutch
  - `nn_xx` - Norwegian
  - `pl_xx` - Polish
  - `pt_xx` - Portuguese
  - `ro_xx` - Romanian
  - `ru_xx` - Russian
  - `sk_xx` - Slovak
  - `sl_xx` - Slovenian
  - `sv_xx` - Swedish
  - `th_xx` - Thai
  - `tr_xx` - Turkish
  - `uk_xx` - Ukrainian
  - `vi_xx` - Vietnamese
  - `zh_CN` - Simplified Chinese
  - `zh_TW` - Traditional Chinese

</p>
</details>
