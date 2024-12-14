# Home Assistant - Tilt Hydrometer - Brewfather
Simple configuration to have Home Assistant pushing Tilt Hydrometer data to Brewfather
These steps might be similar to other apps.

1 - Make sure your Home Assistant has Bluetooth (this might require a little googling or tweaking if running HA on Docker or Kube).
2 - Make sure the system/container your HA is running on your local timezone (otherwise it will report the time on the system timezone as if it was local). Note: The HA timezone will be ignored.
3 - Once HA detects Tilt turn on the tilt integration
4 - Open your HA configuration.yaml and add the following:
```yaml
rest_command:
  tilt_yellow:
    url: http://log.brewfather.net/tilt?id=XXXXXXXXXX
    method: POST
    headers:
      user-agent: 'Home Assistant'
    payload: 'Timepoint={{ ((now().timestamp() - (as_timestamp("1899-12-30"))   ) /86400) }}&SG={{states("sensor.tilt_yellow_specific_gravity")}}&Temp={{states("sensor.tilt_yellow_temperature")}}&Color=YELLOW&Beer=Untitled&Comment=@'
    content_type: 'application/x-www-form-urlencoded; charset=utf-8'
    verify_ssl: false
```
** Replace all occurances of ``tilt_yellow`` with whatever your HA Tilt integration found, replace ``XXXXXXXXXX`` with your Brewfather Tilt Key, replace ``YELLOW`` with your Tilt color.
5 - Create an HA Automation with 4 Time Triggers, for second 0 of every hour (*) for minutes 0, 15, 30 and 45 (As per Brefather DO NOT Update more frequent tha every 15 min) and action (Then Do) ``RESTful Command 'rest_command.tilt_yellow'`` (Replace ``tilt_yellow`` as above)

