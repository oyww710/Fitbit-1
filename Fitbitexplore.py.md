
## Tutorial: Explore your own Fitbit data!

January 21, 2018

### The goal of this project was for me to practice:
1. downloading data from an API 
2. working with new types of data formats (e.g., json)
3. visualizations
4. basic statistics in Python.

#### Also, as a new Fitbit owner (Dec. 2017), previous collegiate athlete, and fitness lover, I was incredibly curious about my own data!

### The general outline of this notebook is as follows:
 1. Import or install necessary libraries
 2. Read in and visualize data sets (heart rate, sleep, steps)
 3. Analyses!
 4. Next steps
 
In order to get my Fitbit data, I first had to set up a Fitbit API. Then, I ran a separate script - available here.

###  1. Import or install necessary libraries


```python
#!pip install fitbit
#!pip install -r requirements/base.txt
#!pip install -r requirements/dev.txt
#!pip install -r requirements/test.txt
from time import sleep
import fitbit
import cherrypy
import requests
import json
import datetime
import scipy.stats
import pandas as pd
import numpy as np

# plotting
import matplotlib
from matplotlib import pyplot as plt
import seaborn as sns
%matplotlib inline
```

Let's read in one file with heartrate data to check the format and see what the data looks like.


```python
with open('heartrate/HR2017-12-23.json') as f:
    hr_dat_sample = json.loads(f.read())
#hr_dat_sample
```







#### That is crazy town output... let's parse the .json file so humans can read it!


```python
parsed_json_hr_samp = json.loads(hr_dat_sample)
#parsed_json_hr_samp
```




    {u'activities-heart': [{u'customHeartRateZones': [],
       u'dateTime': u'2017-12-23',
       u'heartRateZones': [{u'caloriesOut': 1268.603,
         u'max': 96,
         u'min': 30,
         u'minutes': 1227,
         u'name': u'Out of Range'},
        {u'caloriesOut': 572.7827,
         u'max': 134,
         u'min': 96,
         u'minutes': 191,
         u'name': u'Fat Burn'},
        {u'caloriesOut': 54.5846,
         u'max': 163,
         u'min': 134,
         u'minutes': 9,
         u'name': u'Cardio'},
        {u'caloriesOut': 0,
         u'max': 220,
         u'min': 163,
         u'minutes': 0,
         u'name': u'Peak'}],
       u'value': u'83.45'}],
     u'activities-heart-intraday': {u'dataset': [{u'time': u'00:00:00',
        u'value': 92},
       {u'time': u'00:00:03', u'value': 92},
       {u'time': u'00:00:06', u'value': 92},
       {u'time': u'00:00:09', u'value': 92},
       {u'time': u'00:00:12', u'value': 92},
       {u'time': u'00:00:15', u'value': 92},
       {u'time': u'00:00:16', u'value': 95},
       {u'time': u'00:00:17', u'value': 89},
       {u'time': u'00:00:18', u'value': 90},
       {u'time': u'00:00:19', u'value': 91},
       {u'time': u'00:00:21', u'value': 92},
       {u'time': u'00:00:22', u'value': 93},
       {u'time': u'00:00:23', u'value': 95},
       {u'time': u'00:00:24', u'value': 96},
       {u'time': u'00:00:25', u'value': 97},
       {u'time': u'00:00:26', u'value': 98},
       {u'time': u'00:00:27', u'value': 99},
       {u'time': u'00:00:28', u'value': 101},
       {u'time': u'00:00:29', u'value': 102},
       {u'time': u'00:00:31', u'value': 103},
       {u'time': u'00:00:32', u'value': 104},
       {u'time': u'00:00:33', u'value': 103},
       {u'time': u'00:00:36', u'value': 103},
       {u'time': u'00:00:39', u'value': 102},
       {u'time': u'00:00:42', u'value': 102},
       {u'time': u'00:00:43', u'value': 102},
       {u'time': u'00:00:46', u'value': 102},
       {u'time': u'00:00:47', u'value': 103},
       {u'time': u'00:00:50', u'value': 104},
       {u'time': u'00:00:53', u'value': 104},
       {u'time': u'00:00:56', u'value': 104},
       {u'time': u'00:00:59', u'value': 103},
       {u'time': u'00:01:02', u'value': 103},
       {u'time': u'00:01:03', u'value': 103},
       {u'time': u'00:01:06', u'value': 104},
       {u'time': u'00:01:08', u'value': 103},
       {u'time': u'00:01:11', u'value': 103},
       {u'time': u'00:01:14', u'value': 104},
       {u'time': u'00:01:17', u'value': 104},
       {u'time': u'00:01:18', u'value': 103},
       {u'time': u'00:01:21', u'value': 104},
       {u'time': u'00:01:24', u'value': 104},
       {u'time': u'00:01:26', u'value': 103},
       {u'time': u'00:01:29', u'value': 103},
       {u'time': u'00:01:32', u'value': 102},
       {u'time': u'00:01:33', u'value': 103},
       {u'time': u'00:01:36', u'value': 103},
       {u'time': u'00:01:37', u'value': 102},
       {u'time': u'00:01:38', u'value': 102},
       {u'time': u'00:01:41', u'value': 103},
       {u'time': u'00:01:42', u'value': 104},
       {u'time': u'00:01:43', u'value': 103},
       {u'time': u'00:01:46', u'value': 103},
       {u'time': u'00:01:47', u'value': 102},
       {u'time': u'00:01:50', u'value': 102},
       {u'time': u'00:01:53', u'value': 102},
       {u'time': u'00:01:56', u'value': 102},
       {u'time': u'00:01:59', u'value': 102},
       {u'time': u'00:02:00', u'value': 103},
       {u'time': u'00:02:03', u'value': 102},
       {u'time': u'00:02:04', u'value': 102},
       {u'time': u'00:02:07', u'value': 102},
       {u'time': u'00:02:08', u'value': 101},
       {u'time': u'00:02:09', u'value': 100},
       {u'time': u'00:02:12', u'value': 101},
       {u'time': u'00:02:14', u'value': 102},
       {u'time': u'00:02:15', u'value': 103},
       {u'time': u'00:02:18', u'value': 101},
       {u'time': u'00:02:19', u'value': 100},
       {u'time': u'00:02:20', u'value': 99},
       {u'time': u'00:02:22', u'value': 100},
       {u'time': u'00:02:23', u'value': 101},
       {u'time': u'00:02:24', u'value': 102},
       {u'time': u'00:02:25', u'value': 104},
       {u'time': u'00:02:26', u'value': 105},
       {u'time': u'00:02:27', u'value': 106},
       {u'time': u'00:02:28', u'value': 107},
       {u'time': u'00:02:29', u'value': 108},
       {u'time': u'00:02:32', u'value': 108},
       {u'time': u'00:02:35', u'value': 108},
       {u'time': u'00:02:38', u'value': 107},
       {u'time': u'00:02:41', u'value': 107},
       {u'time': u'00:02:42', u'value': 105},
       {u'time': u'00:02:43', u'value': 104},
       {u'time': u'00:02:44', u'value': 103},
       {u'time': u'00:02:45', u'value': 102},
       {u'time': u'00:02:46', u'value': 101},
       {u'time': u'00:02:47', u'value': 101},
       {u'time': u'00:02:48', u'value': 100},
       {u'time': u'00:02:51', u'value': 101},
       {u'time': u'00:02:53', u'value': 102},
       {u'time': u'00:02:56', u'value': 102},
       {u'time': u'00:02:58', u'value': 101},
       {u'time': u'00:03:01', u'value': 101},
       {u'time': u'00:03:02', u'value': 100},
       {u'time': u'00:03:03', u'value': 101},
       {u'time': u'00:03:06', u'value': 99},
       {u'time': u'00:03:07', u'value': 98},
       {u'time': u'00:03:08', u'value': 97},
       {u'time': u'00:03:09', u'value': 96},
       {u'time': u'00:03:10', u'value': 95},
       {u'time': u'00:03:11', u'value': 94},
       {u'time': u'00:03:12', u'value': 92},
       {u'time': u'00:03:13', u'value': 91},
       {u'time': u'00:03:14', u'value': 90},
       {u'time': u'00:03:15', u'value': 89},
       {u'time': u'00:03:16', u'value': 88},
       {u'time': u'00:03:17', u'value': 87},
       {u'time': u'00:03:20', u'value': 85},
       {u'time': u'00:03:21', u'value': 84},
       {u'time': u'00:03:22', u'value': 82},
       {u'time': u'00:03:23', u'value': 81},
       {u'time': u'00:03:26', u'value': 81},
       {u'time': u'00:03:27', u'value': 80},
       {u'time': u'00:03:28', u'value': 79},
       {u'time': u'00:03:31', u'value': 79},
       {u'time': u'00:03:32', u'value': 80},
       {u'time': u'00:03:35', u'value': 80},
       {u'time': u'00:03:36', u'value': 81},
       {u'time': u'00:03:39', u'value': 82},
       {u'time': u'00:03:42', u'value': 82},
       {u'time': u'00:03:45', u'value': 82},
       {u'time': u'00:03:46', u'value': 84},
       {u'time': u'00:03:47', u'value': 88},
       {u'time': u'00:03:48', u'value': 91},
       {u'time': u'00:03:49', u'value': 92},
       {u'time': u'00:03:50', u'value': 93},
       {u'time': u'00:03:53', u'value': 92},
       {u'time': u'00:03:54', u'value': 91},
       {u'time': u'00:03:55', u'value': 91},
       {u'time': u'00:03:56', u'value': 90},
       {u'time': u'00:03:58', u'value': 89},
       {u'time': u'00:03:59', u'value': 90},
       {u'time': u'00:04:01', u'value': 91},
       {u'time': u'00:04:04', u'value': 92},
       {u'time': u'00:04:05', u'value': 93},
       {u'time': u'00:04:08', u'value': 93},
       {u'time': u'00:04:11', u'value': 93},
       {u'time': u'00:04:14', u'value': 93},
       {u'time': u'00:04:15', u'value': 94},
       {u'time': u'00:04:18', u'value': 95},
       {u'time': u'00:04:19', u'value': 95},
       {u'time': u'00:04:21', u'value': 96},
       {u'time': u'00:04:22', u'value': 95},
       {u'time': u'00:04:23', u'value': 96},
       {u'time': u'00:04:25', u'value': 97},
       {u'time': u'00:04:26', u'value': 98},
       {u'time': u'00:04:27', u'value': 92},
       {u'time': u'00:04:28', u'value': 92},
       {u'time': u'00:04:30', u'value': 91},
       {u'time': u'00:04:33', u'value': 91},
       {u'time': u'00:04:36', u'value': 91},
       {u'time': u'00:04:39', u'value': 91},
       {u'time': u'00:04:40', u'value': 92},
       {u'time': u'00:04:43', u'value': 92},
       {u'time': u'00:04:45', u'value': 93},
       {u'time': u'00:04:48', u'value': 93},
       {u'time': u'00:04:49', u'value': 92},
       {u'time': u'00:04:50', u'value': 91},
       {u'time': u'00:04:52', u'value': 90},
       {u'time': u'00:04:53', u'value': 89},
       {u'time': u'00:04:56', u'value': 88},
       {u'time': u'00:04:59', u'value': 88},
       {u'time': u'00:05:02', u'value': 87},
       {u'time': u'00:05:04', u'value': 86},
       {u'time': u'00:05:05', u'value': 86},
       {u'time': u'00:05:07', u'value': 85},
       {u'time': u'00:05:10', u'value': 85},
       {u'time': u'00:05:11', u'value': 85},
       {u'time': u'00:05:14', u'value': 85},
       {u'time': u'00:05:16', u'value': 86},
       {u'time': u'00:05:19', u'value': 86},
       {u'time': u'00:05:21', u'value': 88},
       {u'time': u'00:05:22', u'value': 92},
       {u'time': u'00:05:23', u'value': 94},
       {u'time': u'00:05:24', u'value': 95},
       {u'time': u'00:05:27', u'value': 95},
       {u'time': u'00:05:30', u'value': 95},
       {u'time': u'00:05:32', u'value': 94},
       {u'time': u'00:05:35', u'value': 94},
       {u'time': u'00:05:38', u'value': 94},
       {u'time': u'00:05:39', u'value': 94},
       {u'time': u'00:05:40', u'value': 93},
       {u'time': u'00:05:43', u'value': 93},
       {u'time': u'00:05:46', u'value': 93},
       {u'time': u'00:05:47', u'value': 92},
       {u'time': u'00:05:48', u'value': 92},
       {u'time': u'00:05:49', u'value': 91},
       {u'time': u'00:05:51', u'value': 91},
       {u'time': u'00:05:52', u'value': 86},
       {u'time': u'00:05:53', u'value': 86},
       {u'time': u'00:05:54', u'value': 84},
       {u'time': u'00:05:55', u'value': 84},
       {u'time': u'00:05:58', u'value': 83},
       {u'time': u'00:06:01', u'value': 83},
       {u'time': u'00:06:02', u'value': 83},
       {u'time': u'00:06:05', u'value': 83},
       {u'time': u'00:06:08', u'value': 83},
       {u'time': u'00:06:11', u'value': 83},
       {u'time': u'00:06:14', u'value': 83},
       {u'time': u'00:06:17', u'value': 83},
       {u'time': u'00:06:20', u'value': 83},
       {u'time': u'00:06:23', u'value': 83},
       {u'time': u'00:06:26', u'value': 83},
       {u'time': u'00:06:29', u'value': 83},
       {u'time': u'00:06:31', u'value': 83},
       {u'time': u'00:06:33', u'value': 84},
       {u'time': u'00:06:35', u'value': 85},
       {u'time': u'00:06:38', u'value': 85},
       {u'time': u'00:06:41', u'value': 85},
       {u'time': u'00:06:43', u'value': 84},
       {u'time': u'00:06:46', u'value': 84},
       {u'time': u'00:06:49', u'value': 84},
       {u'time': u'00:06:52', u'value': 84},
       {u'time': u'00:06:55', u'value': 84},
       {u'time': u'00:06:58', u'value': 83},
       {u'time': u'00:06:59', u'value': 84},
       {u'time': u'00:07:02', u'value': 84},
       {u'time': u'00:07:05', u'value': 84},
       {u'time': u'00:07:06', u'value': 85},
       {u'time': u'00:07:09', u'value': 85},
       {u'time': u'00:07:12', u'value': 85},
       {u'time': u'00:07:13', u'value': 85},
       {u'time': u'00:07:15', u'value': 85},
       {u'time': u'00:07:18', u'value': 85},
       {u'time': u'00:07:20', u'value': 86},
       {u'time': u'00:07:23', u'value': 89},
       {u'time': u'00:07:24', u'value': 89},
       {u'time': u'00:07:27', u'value': 89},
       {u'time': u'00:07:28', u'value': 90},
       {u'time': u'00:07:31', u'value': 90},
       {u'time': u'00:07:34', u'value': 91},
       {u'time': u'00:07:36', u'value': 93},
       {u'time': u'00:07:37', u'value': 93},
       {u'time': u'00:07:38', u'value': 92},
       {u'time': u'00:07:40', u'value': 93},
       {u'time': u'00:07:41', u'value': 95},
       {u'time': u'00:07:42', u'value': 97},
       {u'time': u'00:07:43', u'value': 98},
       {u'time': u'00:07:44', u'value': 99},
       {u'time': u'00:07:45', u'value': 100},
       {u'time': u'00:07:47', u'value': 102},
       {u'time': u'00:07:48', u'value': 103},
       {u'time': u'00:07:49', u'value': 105},
       {u'time': u'00:07:52', u'value': 105},
       {u'time': u'00:07:55', u'value': 105},
       {u'time': u'00:07:58', u'value': 105},
       {u'time': u'00:08:00', u'value': 104},
       {u'time': u'00:08:03', u'value': 104},
       {u'time': u'00:08:04', u'value': 104},
       {u'time': u'00:08:07', u'value': 105},
       {u'time': u'00:08:10', u'value': 106},
       {u'time': u'00:08:13', u'value': 106},
       {u'time': u'00:08:14', u'value': 105},
       {u'time': u'00:08:16', u'value': 104},
       {u'time': u'00:08:18', u'value': 103},
       {u'time': u'00:08:20', u'value': 102},
       {u'time': u'00:08:23', u'value': 103},
       {u'time': u'00:08:26', u'value': 102},
       {u'time': u'00:08:28', u'value': 101},
       {u'time': u'00:08:31', u'value': 100},
       {u'time': u'00:08:34', u'value': 100},
       {u'time': u'00:08:35', u'value': 99},
       {u'time': u'00:08:36', u'value': 98},
       {u'time': u'00:08:37', u'value': 98},
       {u'time': u'00:08:38', u'value': 97},
       {u'time': u'00:08:41', u'value': 98},
       {u'time': u'00:08:44', u'value': 97},
       {u'time': u'00:08:46', u'value': 96},
       {u'time': u'00:08:49', u'value': 95},
       {u'time': u'00:08:50', u'value': 96},
       {u'time': u'00:08:52', u'value': 97},
       {u'time': u'00:08:55', u'value': 97},
       {u'time': u'00:08:58', u'value': 96},
       {u'time': u'00:09:00', u'value': 95},
       {u'time': u'00:09:01', u'value': 96},
       {u'time': u'00:09:02', u'value': 95},
       {u'time': u'00:09:05', u'value': 95},
       {u'time': u'00:09:08', u'value': 95},
       {u'time': u'00:09:10', u'value': 96},
       {u'time': u'00:09:12', u'value': 97},
       {u'time': u'00:09:15', u'value': 98},
       {u'time': u'00:09:18', u'value': 97},
       {u'time': u'00:09:20', u'value': 96},
       {u'time': u'00:09:22', u'value': 95},
       {u'time': u'00:09:25', u'value': 95},
       {u'time': u'00:09:26', u'value': 88},
       {u'time': u'00:09:27', u'value': 84},
       {u'time': u'00:09:28', u'value': 84},
       {u'time': u'00:09:30', u'value': 85},
       {u'time': u'00:09:33', u'value': 85},
       {u'time': u'00:09:34', u'value': 83},
       {u'time': u'00:09:35', u'value': 81},
       {u'time': u'00:09:36', u'value': 81},
       {u'time': u'00:09:39', u'value': 81},
       {u'time': u'00:09:42', u'value': 81},
       {u'time': u'00:09:45', u'value': 81},
       {u'time': u'00:09:48', u'value': 80},
       {u'time': u'00:09:51', u'value': 80},
       {u'time': u'00:09:54', u'value': 79},
       {u'time': u'00:09:57', u'value': 79},
       {u'time': u'00:10:00', u'value': 79},
       {u'time': u'00:10:03', u'value': 79},
       {u'time': u'00:10:04', u'value': 79},
       {u'time': u'00:10:07', u'value': 79},
       {u'time': u'00:10:09', u'value': 78},
       {u'time': u'00:10:12', u'value': 79},
       {u'time': u'00:10:15', u'value': 79},
       {u'time': u'00:10:17', u'value': 83},
       {u'time': u'00:10:20', u'value': 83},
       {u'time': u'00:10:23', u'value': 84},
       {u'time': u'00:10:25', u'value': 85},
       {u'time': u'00:10:26', u'value': 86},
       {u'time': u'00:10:29', u'value': 87},
       {u'time': u'00:10:30', u'value': 87},
       {u'time': u'00:10:33', u'value': 87},
       {u'time': u'00:10:36', u'value': 87},
       {u'time': u'00:10:39', u'value': 87},
       {u'time': u'00:10:42', u'value': 87},
       {u'time': u'00:10:45', u'value': 87},
       {u'time': u'00:10:47', u'value': 86},
       {u'time': u'00:10:48', u'value': 87},
       {u'time': u'00:10:51', u'value': 87},
       {u'time': u'00:10:53', u'value': 90},
       {u'time': u'00:10:56', u'value': 90},
       {u'time': u'00:10:59', u'value': 90},
       {u'time': u'00:11:00', u'value': 92},
       {u'time': u'00:11:03', u'value': 92},
       {u'time': u'00:11:04', u'value': 93},
       {u'time': u'00:11:06', u'value': 92},
       {u'time': u'00:11:07', u'value': 92},
       {u'time': u'00:11:10', u'value': 92},
       {u'time': u'00:11:13', u'value': 92},
       {u'time': u'00:11:15', u'value': 91},
       {u'time': u'00:11:18', u'value': 91},
       {u'time': u'00:11:21', u'value': 91},
       {u'time': u'00:11:24', u'value': 92},
       {u'time': u'00:11:26', u'value': 93},
       {u'time': u'00:11:27', u'value': 92},
       {u'time': u'00:11:30', u'value': 92},
       {u'time': u'00:11:33', u'value': 92},
       {u'time': u'00:11:36', u'value': 93},
       {u'time': u'00:11:39', u'value': 94},
       {u'time': u'00:11:42', u'value': 97},
       {u'time': u'00:11:43', u'value': 99},
       {u'time': u'00:11:44', u'value': 101},
       {u'time': u'00:11:46', u'value': 100},
       {u'time': u'00:11:48', u'value': 99},
       {u'time': u'00:11:49', u'value': 98},
       {u'time': u'00:11:51', u'value': 99},
       {u'time': u'00:11:53', u'value': 98},
       {u'time': u'00:11:54', u'value': 97},
       {u'time': u'00:11:55', u'value': 97},
       {u'time': u'00:11:57', u'value': 96},
       {u'time': u'00:12:00', u'value': 96},
       {u'time': u'00:12:01', u'value': 95},
       {u'time': u'00:12:04', u'value': 95},
       {u'time': u'00:12:05', u'value': 94},
       {u'time': u'00:12:06', u'value': 93},
       {u'time': u'00:12:07', u'value': 91},
       {u'time': u'00:12:08', u'value': 90},
       {u'time': u'00:12:09', u'value': 89},
       {u'time': u'00:12:12', u'value': 89},
       {u'time': u'00:12:15', u'value': 89},
       {u'time': u'00:12:17', u'value': 77},
       {u'time': u'00:12:18', u'value': 73},
       {u'time': u'00:12:19', u'value': 70},
       {u'time': u'00:12:20', u'value': 69},
       {u'time': u'00:12:21', u'value': 68},
       {u'time': u'00:12:22', u'value': 67},
       {u'time': u'00:12:24', u'value': 66},
       {u'time': u'00:12:26', u'value': 66},
       {u'time': u'00:12:29', u'value': 66},
       {u'time': u'00:12:32', u'value': 66},
       {u'time': u'00:12:35', u'value': 66},
       {u'time': u'00:12:38', u'value': 66},
       {u'time': u'00:12:41', u'value': 66},
       {u'time': u'00:12:44', u'value': 66},
       {u'time': u'00:12:47', u'value': 66},
       {u'time': u'00:12:50', u'value': 66},
       {u'time': u'00:12:53', u'value': 67},
       {u'time': u'00:12:54', u'value': 68},
       {u'time': u'00:12:55', u'value': 69},
       {u'time': u'00:12:56', u'value': 70},
       {u'time': u'00:12:59', u'value': 69},
       {u'time': u'00:13:02', u'value': 68},
       {u'time': u'00:13:05', u'value': 68},
       {u'time': u'00:13:06', u'value': 67},
       {u'time': u'00:13:08', u'value': 66},
       {u'time': u'00:13:09', u'value': 66},
       {u'time': u'00:13:12', u'value': 66},
       {u'time': u'00:13:13', u'value': 65},
       {u'time': u'00:13:14', u'value': 64},
       {u'time': u'00:13:15', u'value': 63},
       {u'time': u'00:13:17', u'value': 62},
       {u'time': u'00:13:20', u'value': 62},
       {u'time': u'00:13:22', u'value': 63},
       {u'time': u'00:13:25', u'value': 64},
       {u'time': u'00:13:28', u'value': 64},
       {u'time': u'00:13:31', u'value': 65},
       {u'time': u'00:13:33', u'value': 66},
       {u'time': u'00:13:35', u'value': 67},
       {u'time': u'00:13:38', u'value': 67},
       {u'time': u'00:13:39', u'value': 68},
       {u'time': u'00:13:40', u'value': 67},
       {u'time': u'00:13:43', u'value': 68},
       {u'time': u'00:13:46', u'value': 68},
       {u'time': u'00:13:49', u'value': 68},
       {u'time': u'00:13:51', u'value': 67},
       {u'time': u'00:13:54', u'value': 67},
       {u'time': u'00:13:55', u'value': 66},
       {u'time': u'00:13:58', u'value': 66},
       {u'time': u'00:14:00', u'value': 67},
       {u'time': u'00:14:03', u'value': 67},
       {u'time': u'00:14:06', u'value': 67},
       {u'time': u'00:14:09', u'value': 67},
       {u'time': u'00:14:12', u'value': 67},
       {u'time': u'00:14:15', u'value': 67},
       {u'time': u'00:14:18', u'value': 68},
       {u'time': u'00:14:20', u'value': 67},
       {u'time': u'00:14:23', u'value': 67},
       {u'time': u'00:14:26', u'value': 67},
       {u'time': u'00:14:28', u'value': 66},
       {u'time': u'00:14:30', u'value': 67},
       {u'time': u'00:14:33', u'value': 67},
       {u'time': u'00:14:36', u'value': 67},
       {u'time': u'00:14:38', u'value': 66},
       {u'time': u'00:14:41', u'value': 66},
       {u'time': u'00:14:44', u'value': 66},
       {u'time': u'00:14:47', u'value': 66},
       {u'time': u'00:14:50', u'value': 66},
       {u'time': u'00:14:51', u'value': 66},
       {u'time': u'00:14:54', u'value': 66},
       {u'time': u'00:14:57', u'value': 66},
       {u'time': u'00:15:00', u'value': 66},
       {u'time': u'00:15:03', u'value': 66},
       {u'time': u'00:15:04', u'value': 66},
       {u'time': u'00:15:05', u'value': 66},
       {u'time': u'00:15:06', u'value': 65},
       {u'time': u'00:15:09', u'value': 65},
       {u'time': u'00:15:11', u'value': 64},
       {u'time': u'00:15:14', u'value': 64},
       {u'time': u'00:15:17', u'value': 64},
       {u'time': u'00:15:20', u'value': 64},
       {u'time': u'00:15:23', u'value': 64},
       {u'time': u'00:15:26', u'value': 64},
       {u'time': u'00:15:29', u'value': 64},
       {u'time': u'00:15:32', u'value': 64},
       {u'time': u'00:15:35', u'value': 64},
       {u'time': u'00:15:36', u'value': 65},
       {u'time': u'00:15:39', u'value': 65},
       {u'time': u'00:15:41', u'value': 65},
       {u'time': u'00:15:42', u'value': 64},
       {u'time': u'00:15:45', u'value': 63},
       {u'time': u'00:15:48', u'value': 63},
       {u'time': u'00:15:51', u'value': 63},
       {u'time': u'00:15:54', u'value': 63},
       {u'time': u'00:15:55', u'value': 62},
       {u'time': u'00:15:58', u'value': 62},
       {u'time': u'00:15:59', u'value': 63},
       {u'time': u'00:16:01', u'value': 64},
       {u'time': u'00:16:03', u'value': 65},
       {u'time': u'00:16:06', u'value': 66},
       {u'time': u'00:16:09', u'value': 66},
       {u'time': u'00:16:10', u'value': 66},
       {u'time': u'00:16:13', u'value': 66},
       {u'time': u'00:16:16', u'value': 66},
       {u'time': u'00:16:19', u'value': 66},
       {u'time': u'00:16:20', u'value': 65},
       {u'time': u'00:16:23', u'value': 65},
       {u'time': u'00:16:26', u'value': 65},
       {u'time': u'00:16:28', u'value': 64},
       {u'time': u'00:16:31', u'value': 64},
       {u'time': u'00:16:33', u'value': 65},
       {u'time': u'00:16:36', u'value': 65},
       {u'time': u'00:16:39', u'value': 65},
       {u'time': u'00:16:42', u'value': 65},
       {u'time': u'00:16:45', u'value': 65},
       {u'time': u'00:16:46', u'value': 64},
       {u'time': u'00:16:49', u'value': 64},
       {u'time': u'00:16:52', u'value': 64},
       {u'time': u'00:16:55', u'value': 64},
       {u'time': u'00:16:56', u'value': 65},
       {u'time': u'00:16:59', u'value': 65},
       {u'time': u'00:17:01', u'value': 66},
       {u'time': u'00:17:04', u'value': 66},
       {u'time': u'00:17:07', u'value': 66},
       {u'time': u'00:17:10', u'value': 66},
       {u'time': u'00:17:13', u'value': 66},
       {u'time': u'00:17:16', u'value': 66},
       {u'time': u'00:17:17', u'value': 65},
       {u'time': u'00:17:18', u'value': 65},
       {u'time': u'00:17:21', u'value': 65},
       {u'time': u'00:17:23', u'value': 66},
       {u'time': u'00:17:24', u'value': 67},
       {u'time': u'00:17:26', u'value': 68},
       {u'time': u'00:17:27', u'value': 67},
       {u'time': u'00:17:30', u'value': 67},
       {u'time': u'00:17:33', u'value': 66},
       {u'time': u'00:17:36', u'value': 66},
       {u'time': u'00:17:39', u'value': 66},
       {u'time': u'00:17:42', u'value': 66},
       {u'time': u'00:17:44', u'value': 65},
       {u'time': u'00:17:47', u'value': 65},
       {u'time': u'00:17:50', u'value': 65},
       {u'time': u'00:17:53', u'value': 65},
       {u'time': u'00:17:56', u'value': 65},
       {u'time': u'00:17:57', u'value': 66},
       {u'time': u'00:18:00', u'value': 65},
       {u'time': u'00:18:03', u'value': 65},
       {u'time': u'00:18:06', u'value': 65},
       {u'time': u'00:18:09', u'value': 65},
       {u'time': u'00:18:11', u'value': 66},
       {u'time': u'00:18:14', u'value': 66},
       {u'time': u'00:18:17', u'value': 66},
       {u'time': u'00:18:20', u'value': 66},
       {u'time': u'00:18:23', u'value': 66},
       {u'time': u'00:18:26', u'value': 66},
       {u'time': u'00:18:27', u'value': 66},
       {u'time': u'00:18:28', u'value': 65},
       {u'time': u'00:18:31', u'value': 65},
       {u'time': u'00:18:34', u'value': 65},
       {u'time': u'00:18:37', u'value': 65},
       {u'time': u'00:18:40', u'value': 65},
       {u'time': u'00:18:43', u'value': 65},
       {u'time': u'00:18:46', u'value': 65},
       {u'time': u'00:18:49', u'value': 65},
       {u'time': u'00:18:50', u'value': 65},
       {u'time': u'00:18:53', u'value': 65},
       {u'time': u'00:18:56', u'value': 65},
       {u'time': u'00:18:59', u'value': 65},
       {u'time': u'00:19:02', u'value': 65},
       {u'time': u'00:19:05', u'value': 65},
       {u'time': u'00:19:08', u'value': 65},
       {u'time': u'00:19:11', u'value': 65},
       {u'time': u'00:19:14', u'value': 65},
       {u'time': u'00:19:17', u'value': 65},
       {u'time': u'00:19:20', u'value': 65},
       {u'time': u'00:19:23', u'value': 65},
       {u'time': u'00:19:26', u'value': 65},
       {u'time': u'00:19:29', u'value': 65},
       {u'time': u'00:19:32', u'value': 65},
       {u'time': u'00:19:35', u'value': 65},
       {u'time': u'00:19:38', u'value': 65},
       {u'time': u'00:19:40', u'value': 65},
       {u'time': u'00:19:41', u'value': 65},
       {u'time': u'00:19:44', u'value': 65},
       {u'time': u'00:19:47', u'value': 65},
       {u'time': u'00:19:48', u'value': 64},
       {u'time': u'00:19:51', u'value': 64},
       {u'time': u'00:19:54', u'value': 64},
       {u'time': u'00:19:57', u'value': 64},
       {u'time': u'00:20:00', u'value': 64},
       {u'time': u'00:20:03', u'value': 65},
       {u'time': u'00:20:06', u'value': 65},
       {u'time': u'00:20:09', u'value': 65},
       {u'time': u'00:20:12', u'value': 65},
       {u'time': u'00:20:15', u'value': 65},
       {u'time': u'00:20:18', u'value': 65},
       {u'time': u'00:20:21', u'value': 65},
       {u'time': u'00:20:24', u'value': 65},
       {u'time': u'00:20:27', u'value': 65},
       {u'time': u'00:20:30', u'value': 65},
       {u'time': u'00:20:33', u'value': 65},
       {u'time': u'00:20:36', u'value': 65},
       {u'time': u'00:20:39', u'value': 65},
       {u'time': u'00:20:42', u'value': 65},
       {u'time': u'00:20:45', u'value': 66},
       {u'time': u'00:20:48', u'value': 66},
       {u'time': u'00:20:51', u'value': 66},
       {u'time': u'00:20:54', u'value': 66},
       {u'time': u'00:20:57', u'value': 66},
       {u'time': u'00:20:58', u'value': 66},
       {u'time': u'00:21:01', u'value': 66},
       {u'time': u'00:21:04', u'value': 66},
       {u'time': u'00:21:07', u'value': 66},
       {u'time': u'00:21:10', u'value': 66},
       {u'time': u'00:21:13', u'value': 66},
       {u'time': u'00:21:16', u'value': 66},
       {u'time': u'00:21:17', u'value': 67},
       {u'time': u'00:21:20', u'value': 67},
       {u'time': u'00:21:23', u'value': 67},
       {u'time': u'00:21:26', u'value': 67},
       {u'time': u'00:21:29', u'value': 67},
       {u'time': u'00:21:32', u'value': 67},
       {u'time': u'00:21:35', u'value': 66},
       {u'time': u'00:21:38', u'value': 66},
       {u'time': u'00:21:39', u'value': 65},
       {u'time': u'00:21:42', u'value': 66},
       {u'time': u'00:21:45', u'value': 66},
       {u'time': u'00:21:48', u'value': 66},
       {u'time': u'00:21:51', u'value': 66},
       {u'time': u'00:21:54', u'value': 66},
       {u'time': u'00:21:57', u'value': 66},
       {u'time': u'00:22:00', u'value': 66},
       {u'time': u'00:22:03', u'value': 66},
       {u'time': u'00:22:06', u'value': 66},
       {u'time': u'00:22:09', u'value': 66},
       {u'time': u'00:22:11', u'value': 65},
       {u'time': u'00:22:12', u'value': 65},
       {u'time': u'00:22:15', u'value': 65},
       {u'time': u'00:22:18', u'value': 65},
       {u'time': u'00:22:21', u'value': 65},
       {u'time': u'00:22:24', u'value': 65},
       {u'time': u'00:22:27', u'value': 65},
       {u'time': u'00:22:28', u'value': 66},
       {u'time': u'00:22:30', u'value': 65},
       {u'time': u'00:22:33', u'value': 65},
       {u'time': u'00:22:36', u'value': 65},
       {u'time': u'00:22:39', u'value': 65},
       {u'time': u'00:22:42', u'value': 65},
       {u'time': u'00:22:45', u'value': 65},
       {u'time': u'00:22:48', u'value': 65},
       {u'time': u'00:22:51', u'value': 65},
       {u'time': u'00:22:54', u'value': 65},
       {u'time': u'00:22:57', u'value': 65},
       {u'time': u'00:22:58', u'value': 64},
       {u'time': u'00:23:00', u'value': 65},
       {u'time': u'00:23:02', u'value': 65},
       {u'time': u'00:23:03', u'value': 66},
       {u'time': u'00:23:06', u'value': 65},
       {u'time': u'00:23:09', u'value': 65},
       {u'time': u'00:23:11', u'value': 64},
       {u'time': u'00:23:13', u'value': 65},
       {u'time': u'00:23:16', u'value': 65},
       {u'time': u'00:23:17', u'value': 66},
       {u'time': u'00:23:18', u'value': 66},
       {u'time': u'00:23:21', u'value': 66},
       {u'time': u'00:23:24', u'value': 65},
       {u'time': u'00:23:25', u'value': 66},
       {u'time': u'00:23:26', u'value': 67},
       {u'time': u'00:23:27', u'value': 68},
       {u'time': u'00:23:30', u'value': 69},
       {u'time': u'00:23:32', u'value': 68},
       {u'time': u'00:23:35', u'value': 67},
       {u'time': u'00:23:37', u'value': 66},
       {u'time': u'00:23:39', u'value': 65},
       {u'time': u'00:23:42', u'value': 65},
       {u'time': u'00:23:43', u'value': 64},
       {u'time': u'00:23:46', u'value': 64},
       {u'time': u'00:23:49', u'value': 64},
       {u'time': u'00:23:52', u'value': 63},
       {u'time': u'00:23:55', u'value': 63},
       {u'time': u'00:23:56', u'value': 62},
       {u'time': u'00:23:59', u'value': 62},
       {u'time': u'00:24:01', u'value': 63},
       {u'time': u'00:24:04', u'value': 63},
       {u'time': u'00:24:07', u'value': 63},
       {u'time': u'00:24:09', u'value': 64},
       {u'time': u'00:24:12', u'value': 64},
       {u'time': u'00:24:15', u'value': 64},
       {u'time': u'00:24:18', u'value': 64},
       {u'time': u'00:24:21', u'value': 64},
       {u'time': u'00:24:22', u'value': 64},
       {u'time': u'00:24:25', u'value': 64},
       {u'time': u'00:24:28', u'value': 64},
       {u'time': u'00:24:31', u'value': 65},
       {u'time': u'00:24:34', u'value': 65},
       {u'time': u'00:24:37', u'value': 65},
       {u'time': u'00:24:40', u'value': 65},
       {u'time': u'00:24:43', u'value': 65},
       {u'time': u'00:24:46', u'value': 65},
       {u'time': u'00:24:49', u'value': 65},
       {u'time': u'00:24:52', u'value': 65},
       {u'time': u'00:24:55', u'value': 65},
       {u'time': u'00:24:58', u'value': 65},
       {u'time': u'00:25:01', u'value': 65},
       {u'time': u'00:25:02', u'value': 66},
       {u'time': u'00:25:04', u'value': 67},
       {u'time': u'00:25:07', u'value': 67},
       {u'time': u'00:25:08', u'value': 66},
       {u'time': u'00:25:11', u'value': 66},
       {u'time': u'00:25:13', u'value': 65},
       {u'time': u'00:25:14', u'value': 66},
       {u'time': u'00:25:17', u'value': 66},
       {u'time': u'00:25:20', u'value': 66},
       {u'time': u'00:25:22', u'value': 65},
       {u'time': u'00:25:24', u'value': 65},
       {u'time': u'00:25:27', u'value': 65},
       {u'time': u'00:25:29', u'value': 66},
       {u'time': u'00:25:30', u'value': 66},
       {u'time': u'00:25:32', u'value': 65},
       {u'time': u'00:25:34', u'value': 65},
       {u'time': u'00:25:36', u'value': 66},
       {u'time': u'00:25:39', u'value': 66},
       {u'time': u'00:25:42', u'value': 66},
       {u'time': u'00:25:45', u'value': 66},
       {u'time': u'00:25:48', u'value': 67},
       {u'time': u'00:25:50', u'value': 66},
       {u'time': u'00:25:53', u'value': 66},
       {u'time': u'00:25:56', u'value': 65},
       {u'time': u'00:25:59', u'value': 65},
       {u'time': u'00:26:02', u'value': 65},
       {u'time': u'00:26:05', u'value': 65},
       {u'time': u'00:26:08', u'value': 65},
       {u'time': u'00:26:10', u'value': 64},
       {u'time': u'00:26:13', u'value': 64},
       {u'time': u'00:26:14', u'value': 65},
       {u'time': u'00:26:17', u'value': 65},
       {u'time': u'00:26:19', u'value': 66},
       {u'time': u'00:26:22', u'value': 66},
       {u'time': u'00:26:25', u'value': 66},
       {u'time': u'00:26:28', u'value': 65},
       {u'time': u'00:26:31', u'value': 65},
       {u'time': u'00:26:34', u'value': 65},
       {u'time': u'00:26:37', u'value': 65},
       {u'time': u'00:26:40', u'value': 65},
       {u'time': u'00:26:41', u'value': 65},
       {u'time': u'00:26:44', u'value': 65},
       {u'time': u'00:26:47', u'value': 65},
       {u'time': u'00:26:50', u'value': 65},
       {u'time': u'00:26:53', u'value': 65},
       {u'time': u'00:26:54', u'value': 66},
       {u'time': u'00:26:57', u'value': 66},
       {u'time': u'00:27:00', u'value': 66},
       {u'time': u'00:27:03', u'value': 66},
       {u'time': u'00:27:06', u'value': 66},
       {u'time': u'00:27:09', u'value': 66},
       {u'time': u'00:27:10', u'value': 65},
       {u'time': u'00:27:13', u'value': 65},
       {u'time': u'00:27:16', u'value': 65},
       {u'time': u'00:27:19', u'value': 65},
       {u'time': u'00:27:22', u'value': 65},
       {u'time': u'00:27:25', u'value': 65},
       {u'time': u'00:27:28', u'value': 65},
       {u'time': u'00:27:31', u'value': 65},
       {u'time': u'00:27:32', u'value': 66},
       {u'time': u'00:27:35', u'value': 66},
       {u'time': u'00:27:38', u'value': 66},
       {u'time': u'00:27:41', u'value': 66},
       {u'time': u'00:27:42', u'value': 65},
       {u'time': u'00:27:43', u'value': 65},
       {u'time': u'00:27:46', u'value': 65},
       {u'time': u'00:27:49', u'value': 65},
       {u'time': u'00:27:50', u'value': 65},
       {u'time': u'00:27:51', u'value': 65},
       {u'time': u'00:27:54', u'value': 65},
       {u'time': u'00:27:57', u'value': 65},
       {u'time': u'00:28:00', u'value': 65},
       {u'time': u'00:28:03', u'value': 65},
       {u'time': u'00:28:06', u'value': 65},
       {u'time': u'00:28:07', u'value': 65},
       {u'time': u'00:28:10', u'value': 65},
       {u'time': u'00:28:11', u'value': 64},
       {u'time': u'00:28:14', u'value': 64},
       {u'time': u'00:28:17', u'value': 64},
       {u'time': u'00:28:20', u'value': 64},
       {u'time': u'00:28:21', u'value': 65},
       {u'time': u'00:28:24', u'value': 65},
       {u'time': u'00:28:25', u'value': 65},
       {u'time': u'00:28:28', u'value': 66},
       {u'time': u'00:28:30', u'value': 65},
       {u'time': u'00:28:33', u'value': 64},
       {u'time': u'00:28:36', u'value': 64},
       {u'time': u'00:28:39', u'value': 64},
       {u'time': u'00:28:42', u'value': 64},
       {u'time': u'00:28:44', u'value': 65},
       {u'time': u'00:28:47', u'value': 65},
       {u'time': u'00:28:48', u'value': 66},
       {u'time': u'00:28:51', u'value': 66},
       {u'time': u'00:28:54', u'value': 66},
       {u'time': u'00:28:55', u'value': 66},
       {u'time': u'00:28:56', u'value': 65},
       {u'time': u'00:28:59', u'value': 65},
       {u'time': u'00:29:01', u'value': 66},
       {u'time': u'00:29:04', u'value': 66},
       {u'time': u'00:29:07', u'value': 66},
       {u'time': u'00:29:10', u'value': 66},
       {u'time': u'00:29:13', u'value': 66},
       {u'time': u'00:29:16', u'value': 66},
       {u'time': u'00:29:19', u'value': 66},
       {u'time': u'00:29:20', u'value': 67},
       {u'time': u'00:29:22', u'value': 66},
       {u'time': u'00:29:25', u'value': 67},
       {u'time': u'00:29:27', u'value': 66},
       {u'time': u'00:29:30', u'value': 66},
       {u'time': u'00:29:33', u'value': 66},
       {u'time': u'00:29:36', u'value': 66},
       {u'time': u'00:29:38', u'value': 66},
       {u'time': u'00:29:41', u'value': 66},
       {u'time': u'00:29:44', u'value': 67},
       {u'time': u'00:29:45', u'value': 68},
       {u'time': u'00:29:47', u'value': 69},
       {u'time': u'00:29:50', u'value': 69},
       {u'time': u'00:29:53', u'value': 68},
       {u'time': u'00:29:56', u'value': 68},
       {u'time': u'00:29:57', u'value': 67},
       {u'time': u'00:30:00', u'value': 67},
       {u'time': u'00:30:01', u'value': 67},
       {u'time': u'00:30:02', u'value': 66},
       {u'time': u'00:30:05', u'value': 66},
       {u'time': u'00:30:08', u'value': 66},
       {u'time': u'00:30:11', u'value': 67},
       {u'time': u'00:30:14', u'value': 67},
       {u'time': u'00:30:17', u'value': 67},
       {u'time': u'00:30:20', u'value': 67},
       {u'time': u'00:30:23', u'value': 67},
       {u'time': u'00:30:26', u'value': 67},
       {u'time': u'00:30:29', u'value': 66},
       {u'time': u'00:30:32', u'value': 66},
       {u'time': u'00:30:35', u'value': 66},
       {u'time': u'00:30:38', u'value': 66},
       {u'time': u'00:30:41', u'value': 66},
       {u'time': u'00:30:44', u'value': 66},
       {u'time': u'00:30:47', u'value': 66},
       {u'time': u'00:30:50', u'value': 66},
       {u'time': u'00:30:53', u'value': 66},
       {u'time': u'00:30:56', u'value': 66},
       {u'time': u'00:30:59', u'value': 66},
       {u'time': u'00:31:02', u'value': 66},
       {u'time': u'00:31:03', u'value': 66},
       {u'time': u'00:31:06', u'value': 66},
       {u'time': u'00:31:07', u'value': 67},
       {u'time': u'00:31:08', u'value': 68},
       {u'time': u'00:31:11', u'value': 68},
       {u'time': u'00:31:12', u'value': 68},
       {u'time': u'00:31:15', u'value': 68},
       {u'time': u'00:31:18', u'value': 68},
       {u'time': u'00:31:21', u'value': 68},
       {u'time': u'00:31:22', u'value': 67},
       {u'time': u'00:31:25', u'value': 67},
       {u'time': u'00:31:28', u'value': 67},
       {u'time': u'00:31:31', u'value': 67},
       {u'time': u'00:31:34', u'value': 67},
       {u'time': u'00:31:37', u'value': 67},
       {u'time': u'00:31:40', u'value': 67},
       {u'time': u'00:31:43', u'value': 66},
       {u'time': u'00:31:46', u'value': 66},
       {u'time': u'00:31:49', u'value': 66},
       {u'time': u'00:31:52', u'value': 66},
       {u'time': u'00:31:55', u'value': 66},
       {u'time': u'00:31:58', u'value': 66},
       {u'time': u'00:32:01', u'value': 66},
       {u'time': u'00:32:04', u'value': 66},
       {u'time': u'00:32:07', u'value': 66},
       {u'time': u'00:32:10', u'value': 66},
       {u'time': u'00:32:13', u'value': 66},
       {u'time': u'00:32:15', u'value': 65},
       {u'time': u'00:32:18', u'value': 65},
       {u'time': u'00:32:21', u'value': 66},
       {u'time': u'00:32:24', u'value': 66},
       {u'time': u'00:32:25', u'value': 67},
       {u'time': u'00:32:26', u'value': 67},
       {u'time': u'00:32:29', u'value': 67},
       {u'time': u'00:32:32', u'value': 67},
       {u'time': u'00:32:35', u'value': 67},
       {u'time': u'00:32:38', u'value': 67},
       {u'time': u'00:32:41', u'value': 67},
       {u'time': u'00:32:44', u'value': 67},
       {u'time': u'00:32:47', u'value': 67},
       {u'time': u'00:32:50', u'value': 67},
       {u'time': u'00:32:53', u'value': 67},
       {u'time': u'00:32:56', u'value': 67},
       {u'time': u'00:32:58', u'value': 68},
       {u'time': u'00:33:01', u'value': 68},
       {u'time': u'00:33:04', u'value': 68},
       {u'time': u'00:33:07', u'value': 68},
       {u'time': u'00:33:08', u'value': 69},
       {u'time': u'00:33:11', u'value': 69},
       {u'time': u'00:33:14', u'value': 69},
       {u'time': u'00:33:17', u'value': 69},
       {u'time': u'00:33:20', u'value': 69},
       {u'time': u'00:33:23', u'value': 69},
       {u'time': u'00:33:26', u'value': 69},
       {u'time': u'00:33:29', u'value': 69},
       {u'time': u'00:33:32', u'value': 69},
       {u'time': u'00:33:35', u'value': 70},
       {u'time': u'00:33:38', u'value': 70},
       {u'time': u'00:33:41', u'value': 70},
       {u'time': u'00:33:42', u'value': 70},
       {u'time': u'00:33:44', u'value': 70},
       {u'time': u'00:33:47', u'value': 70},
       {u'time': u'00:33:50', u'value': 70},
       {u'time': u'00:33:53', u'value': 70},
       {u'time': u'00:33:56', u'value': 70},
       {u'time': u'00:33:59', u'value': 70},
       {u'time': u'00:34:02', u'value': 70},
       {u'time': u'00:34:05', u'value': 70},
       {u'time': u'00:34:08', u'value': 70},
       {u'time': u'00:34:11', u'value': 70},
       {u'time': u'00:34:14', u'value': 70},
       {u'time': u'00:34:17', u'value': 70},
       {u'time': u'00:34:20', u'value': 70},
       {u'time': u'00:34:23', u'value': 70},
       {u'time': u'00:34:26', u'value': 70},
       {u'time': u'00:34:29', u'value': 70},
       {u'time': u'00:34:32', u'value': 70},
       {u'time': u'00:34:35', u'value': 70},
       {u'time': u'00:34:38', u'value': 70},
       {u'time': u'00:34:41', u'value': 70},
       {u'time': u'00:34:44', u'value': 70},
       {u'time': u'00:34:45', u'value': 70},
       {u'time': u'00:34:48', u'value': 70},
       {u'time': u'00:34:51', u'value': 70},
       {u'time': u'00:34:54', u'value': 70},
       {u'time': u'00:34:57', u'value': 70},
       {u'time': u'00:34:58', u'value': 70},
       {u'time': u'00:35:01', u'value': 70},
       {u'time': u'00:35:04', u'value': 70},
       {u'time': u'00:35:07', u'value': 71},
       {u'time': u'00:35:10', u'value': 71},
       {u'time': u'00:35:13', u'value': 71},
       {u'time': u'00:35:16', u'value': 71},
       {u'time': u'00:35:19', u'value': 71},
       {u'time': u'00:35:22', u'value': 71},
       {u'time': u'00:35:25', u'value': 72},
       {u'time': u'00:35:26', u'value': 71},
       {u'time': u'00:35:29', u'value': 71},
       {u'time': u'00:35:32', u'value': 71},
       {u'time': u'00:35:35', u'value': 71},
       {u'time': u'00:35:38', u'value': 71},
       {u'time': u'00:35:41', u'value': 71},
       {u'time': u'00:35:44', u'value': 71},
       {u'time': u'00:35:47', u'value': 71},
       {u'time': u'00:35:50', u'value': 71},
       {u'time': u'00:35:53', u'value': 71},
       {u'time': u'00:35:56', u'value': 71},
       {u'time': u'00:35:59', u'value': 71},
       {u'time': u'00:36:02', u'value': 71},
       {u'time': u'00:36:05', u'value': 71},
       {u'time': u'00:36:08', u'value': 71},
       {u'time': u'00:36:11', u'value': 71},
       {u'time': u'00:36:14', u'value': 71},
       {u'time': u'00:36:15', u'value': 71},
       {u'time': u'00:36:16', u'value': 71},
       {u'time': u'00:36:19', u'value': 71},
       {u'time': u'00:36:21', u'value': 70},
       {u'time': u'00:36:24', u'value': 69},
       {u'time': u'00:36:27', u'value': 69},
       {u'time': u'00:36:28', u'value': 68},
       {u'time': u'00:36:29', u'value': 67},
       {u'time': u'00:36:32', u'value': 67},
       {u'time': u'00:36:34', u'value': 66},
       {u'time': u'00:36:37', u'value': 66},
       {u'time': u'00:36:40', u'value': 67},
       {u'time': u'00:36:43', u'value': 68},
       {u'time': u'00:36:44', u'value': 67},
       {u'time': u'00:36:47', u'value': 67},
       {u'time': u'00:36:50', u'value': 67},
       {u'time': u'00:36:53', u'value': 67},
       {u'time': u'00:36:54', u'value': 67},
       {u'time': u'00:36:57', u'value': 67},
       {u'time': u'00:37:00', u'value': 67},
       {u'time': u'00:37:03', u'value': 67},
       {u'time': u'00:37:06', u'value': 67},
       {u'time': u'00:37:09', u'value': 67},
       {u'time': u'00:37:12', u'value': 67},
       {u'time': u'00:37:15', u'value': 67},
       {u'time': u'00:37:18', u'value': 67},
       {u'time': u'00:37:21', u'value': 67},
       {u'time': u'00:37:22', u'value': 67},
       {u'time': u'00:37:25', u'value': 67},
       {u'time': u'00:37:28', u'value': 67},
       {u'time': u'00:37:31', u'value': 67},
       {u'time': u'00:37:34', u'value': 67},
       {u'time': u'00:37:37', u'value': 67},
       {u'time': u'00:37:40', u'value': 67},
       {u'time': u'00:37:43', u'value': 67},
       {u'time': u'00:37:46', u'value': 67},
       {u'time': u'00:37:49', u'value': 67},
       {u'time': u'00:37:52', u'value': 67},
       {u'time': u'00:37:55', u'value': 67},
       {u'time': u'00:37:58', u'value': 67},
       {u'time': u'00:38:01', u'value': 67},
       {u'time': u'00:38:04', u'value': 67},
       {u'time': u'00:38:07', u'value': 67},
       {u'time': u'00:38:09', u'value': 68},
       {u'time': u'00:38:12', u'value': 68},
       {u'time': u'00:38:15', u'value': 68},
       {u'time': u'00:38:18', u'value': 68},
       {u'time': u'00:38:21', u'value': 68},
       {u'time': u'00:38:24', u'value': 66},
       {u'time': u'00:38:27', u'value': 66},
       {u'time': u'00:38:30', u'value': 66},
       {u'time': u'00:38:32', u'value': 65},
       {u'time': u'00:38:35', u'value': 65},
       {u'time': u'00:38:36', u'value': 66},
       {u'time': u'00:38:37', u'value': 66},
       {u'time': u'00:38:40', u'value': 66},
       {u'time': u'00:38:43', u'value': 66},
       {u'time': u'00:38:46', u'value': 66},
       {u'time': u'00:38:49', u'value': 66},
       {u'time': u'00:38:52', u'value': 66},
       {u'time': u'00:38:55', u'value': 66},
       {u'time': u'00:38:58', u'value': 66},
       {u'time': u'00:39:01', u'value': 66},
       {u'time': u'00:39:04', u'value': 66},
       {u'time': u'00:39:07', u'value': 66},
       {u'time': u'00:39:10', u'value': 66},
       {u'time': u'00:39:13', u'value': 66},
       {u'time': u'00:39:15', u'value': 66},
       {u'time': u'00:39:18', u'value': 66},
       {u'time': u'00:39:21', u'value': 66},
       {u'time': u'00:39:24', u'value': 66},
       {u'time': u'00:39:27', u'value': 66},
       {u'time': u'00:39:30', u'value': 66},
       {u'time': u'00:39:33', u'value': 66},
       {u'time': u'00:39:36', u'value': 66},
       {u'time': u'00:39:39', u'value': 66},
       {u'time': u'00:39:42', u'value': 66},
       ...],
      u'datasetInterval': 1,
      u'datasetType': u'second'}}




```python
list(parsed_json_hr_samp['activities-heart-intraday'].keys())
```




    [u'datasetType', u'datasetInterval', u'dataset']



Much better. Now we can see the headings inside 'activities-heart-intraday'. 

## 2. Read in and visualize data

## Heart Rate


```python
dates = pd.date_range('2017-12-23', '2018-01-25')
hrval = []

for date in dates:
    fname = 'heartrate/HR' + date.strftime('%Y-%m-%d') + '.json'
    with open(fname) as f:
        date_data = json.loads(f.read())
        
        data = pd.read_json(date_data, typ='series')
        hrval.append(data['activities-heart-intraday']['dataset'][1])

HR_df = pd.DataFrame(hrval,index = dates)
HR_df.columns = ['time', 'bpm']
```


```python
HR_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>time</th>
      <th>bpm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-23</th>
      <td>00:00:03</td>
      <td>92</td>
    </tr>
    <tr>
      <th>2017-12-24</th>
      <td>00:00:04</td>
      <td>88</td>
    </tr>
    <tr>
      <th>2017-12-25</th>
      <td>00:00:03</td>
      <td>94</td>
    </tr>
    <tr>
      <th>2017-12-26</th>
      <td>00:00:03</td>
      <td>93</td>
    </tr>
    <tr>
      <th>2017-12-27</th>
      <td>00:00:03</td>
      <td>74</td>
    </tr>
  </tbody>
</table>
</div>



This should be where the heart rate statistics are stored. Let's check.


```python
stats = data['activities-heart-intraday']['dataset']
HR=pd.DataFrame(stats)
HR.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>time</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>00:00:01</td>
      <td>68</td>
    </tr>
    <tr>
      <th>1</th>
      <td>00:00:02</td>
      <td>69</td>
    </tr>
    <tr>
      <th>2</th>
      <td>00:00:05</td>
      <td>70</td>
    </tr>
    <tr>
      <th>3</th>
      <td>00:00:08</td>
      <td>70</td>
    </tr>
    <tr>
      <th>4</th>
      <td>00:00:11</td>
      <td>69</td>
    </tr>
  </tbody>
</table>
</div>



Hooray! Alas, they are here. Let's rename the columns and plot data just to have a look.


```python
HR.columns = ['time', 'bpm']
HR.plot(color = "firebrick")
plt.ylabel('Heart Rate');
```


![png](output_16_0.png)


That's a lot of data... it's my heartrate for every few seconds for an entire day. 

Hmmmm... it looks like I went for a run or swim at some point!

My researcher instincts are coming out now... Must check for outliers and see the general range of heartrate values!


```python
HR.describe()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>bpm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>25694.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>82.025181</td>
    </tr>
    <tr>
      <th>std</th>
      <td>18.923871</td>
    </tr>
    <tr>
      <th>min</th>
      <td>55.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>70.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>78.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>87.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>204.000000</td>
    </tr>
  </tbody>
</table>
</div>



## Sleep Data

Let's repeat the data structure exploration that we did with heartrate for sleep! This code should read in one sleep file, should get the highest level keys: 'summary' and 'sleep'



```python
with open('sleep/sleep2017-12-23.json') as f:
    sleepdat = json.loads(f.read())
sleepdata = pd.read_json(sleepdat, typ='series')
sleepdata
```




    sleep      [{u'logId': 16665472783, u'dateOfSleep': u'201...
    summary    {u'totalTimeInBed': 547, u'stages': {u'light':...
    dtype: object




```python
parsed_json = json.loads(sleepdat)
#print(json.dumps(parsed_json))
```

    {"sleep": [{"logId": 16665472783, "dateOfSleep": "2017-12-23", "minutesToFallAsleep": 0, "awakeningsCount": 9, "minutesAwake": 19, "timeInBed": 547, "minutesAsleep": 524, "awakeDuration": 11, "efficiency": 97, "isMainSleep": true, "startTime": "2017-12-23T00:40:00.000", "restlessCount": 7, "duration": 32820000, "restlessDuration": 12, "minuteData": [{"value": "2", "dateTime": "00:40:00"}, {"value": "3", "dateTime": "00:41:00"}, {"value": "2", "dateTime": "00:42:00"}, {"value": "2", "dateTime": "00:43:00"}, {"value": "1", "dateTime": "00:44:00"}, {"value": "1", "dateTime": "00:45:00"}, {"value": "1", "dateTime": "00:46:00"}, {"value": "1", "dateTime": "00:47:00"}, {"value": "1", "dateTime": "00:48:00"}, {"value": "1", "dateTime": "00:49:00"}, {"value": "1", "dateTime": "00:50:00"}, {"value": "1", "dateTime": "00:51:00"}, {"value": "1", "dateTime": "00:52:00"}, {"value": "1", "dateTime": "00:53:00"}, {"value": "1", "dateTime": "00:54:00"}, {"value": "1", "dateTime": "00:55:00"}, {"value": "1", "dateTime": "00:56:00"}, {"value": "1", "dateTime": "00:57:00"}, {"value": "1", "dateTime": "00:58:00"}, {"value": "1", "dateTime": "00:59:00"}, {"value": "1", "dateTime": "01:00:00"}, {"value": "1", "dateTime": "01:01:00"}, {"value": "1", "dateTime": "01:02:00"}, {"value": "1", "dateTime": "01:03:00"}, {"value": "1", "dateTime": "01:04:00"}, {"value": "1", "dateTime": "01:05:00"}, {"value": "1", "dateTime": "01:06:00"}, {"value": "1", "dateTime": "01:07:00"}, {"value": "1", "dateTime": "01:08:00"}, {"value": "1", "dateTime": "01:09:00"}, {"value": "1", "dateTime": "01:10:00"}, {"value": "1", "dateTime": "01:11:00"}, {"value": "1", "dateTime": "01:12:00"}, {"value": "1", "dateTime": "01:13:00"}, {"value": "1", "dateTime": "01:14:00"}, {"value": "1", "dateTime": "01:15:00"}, {"value": "1", "dateTime": "01:16:00"}, {"value": "1", "dateTime": "01:17:00"}, {"value": "1", "dateTime": "01:18:00"}, {"value": "1", "dateTime": "01:19:00"}, {"value": "1", "dateTime": "01:20:00"}, {"value": "1", "dateTime": "01:21:00"}, {"value": "1", "dateTime": "01:22:00"}, {"value": "1", "dateTime": "01:23:00"}, {"value": "1", "dateTime": "01:24:00"}, {"value": "1", "dateTime": "01:25:00"}, {"value": "1", "dateTime": "01:26:00"}, {"value": "1", "dateTime": "01:27:00"}, {"value": "1", "dateTime": "01:28:00"}, {"value": "1", "dateTime": "01:29:00"}, {"value": "1", "dateTime": "01:30:00"}, {"value": "1", "dateTime": "01:31:00"}, {"value": "1", "dateTime": "01:32:00"}, {"value": "1", "dateTime": "01:33:00"}, {"value": "1", "dateTime": "01:34:00"}, {"value": "1", "dateTime": "01:35:00"}, {"value": "1", "dateTime": "01:36:00"}, {"value": "1", "dateTime": "01:37:00"}, {"value": "1", "dateTime": "01:38:00"}, {"value": "1", "dateTime": "01:39:00"}, {"value": "1", "dateTime": "01:40:00"}, {"value": "1", "dateTime": "01:41:00"}, {"value": "1", "dateTime": "01:42:00"}, {"value": "1", "dateTime": "01:43:00"}, {"value": "1", "dateTime": "01:44:00"}, {"value": "1", "dateTime": "01:45:00"}, {"value": "1", "dateTime": "01:46:00"}, {"value": "1", "dateTime": "01:47:00"}, {"value": "1", "dateTime": "01:48:00"}, {"value": "1", "dateTime": "01:49:00"}, {"value": "1", "dateTime": "01:50:00"}, {"value": "1", "dateTime": "01:51:00"}, {"value": "1", "dateTime": "01:52:00"}, {"value": "1", "dateTime": "01:53:00"}, {"value": "1", "dateTime": "01:54:00"}, {"value": "1", "dateTime": "01:55:00"}, {"value": "1", "dateTime": "01:56:00"}, {"value": "1", "dateTime": "01:57:00"}, {"value": "1", "dateTime": "01:58:00"}, {"value": "1", "dateTime": "01:59:00"}, {"value": "1", "dateTime": "02:00:00"}, {"value": "1", "dateTime": "02:01:00"}, {"value": "1", "dateTime": "02:02:00"}, {"value": "1", "dateTime": "02:03:00"}, {"value": "1", "dateTime": "02:04:00"}, {"value": "1", "dateTime": "02:05:00"}, {"value": "1", "dateTime": "02:06:00"}, {"value": "1", "dateTime": "02:07:00"}, {"value": "1", "dateTime": "02:08:00"}, {"value": "1", "dateTime": "02:09:00"}, {"value": "1", "dateTime": "02:10:00"}, {"value": "1", "dateTime": "02:11:00"}, {"value": "1", "dateTime": "02:12:00"}, {"value": "1", "dateTime": "02:13:00"}, {"value": "1", "dateTime": "02:14:00"}, {"value": "1", "dateTime": "02:15:00"}, {"value": "1", "dateTime": "02:16:00"}, {"value": "1", "dateTime": "02:17:00"}, {"value": "1", "dateTime": "02:18:00"}, {"value": "1", "dateTime": "02:19:00"}, {"value": "1", "dateTime": "02:20:00"}, {"value": "1", "dateTime": "02:21:00"}, {"value": "1", "dateTime": "02:22:00"}, {"value": "1", "dateTime": "02:23:00"}, {"value": "1", "dateTime": "02:24:00"}, {"value": "1", "dateTime": "02:25:00"}, {"value": "1", "dateTime": "02:26:00"}, {"value": "1", "dateTime": "02:27:00"}, {"value": "1", "dateTime": "02:28:00"}, {"value": "1", "dateTime": "02:29:00"}, {"value": "1", "dateTime": "02:30:00"}, {"value": "1", "dateTime": "02:31:00"}, {"value": "1", "dateTime": "02:32:00"}, {"value": "1", "dateTime": "02:33:00"}, {"value": "1", "dateTime": "02:34:00"}, {"value": "1", "dateTime": "02:35:00"}, {"value": "1", "dateTime": "02:36:00"}, {"value": "1", "dateTime": "02:37:00"}, {"value": "1", "dateTime": "02:38:00"}, {"value": "1", "dateTime": "02:39:00"}, {"value": "1", "dateTime": "02:40:00"}, {"value": "1", "dateTime": "02:41:00"}, {"value": "1", "dateTime": "02:42:00"}, {"value": "1", "dateTime": "02:43:00"}, {"value": "1", "dateTime": "02:44:00"}, {"value": "1", "dateTime": "02:45:00"}, {"value": "1", "dateTime": "02:46:00"}, {"value": "1", "dateTime": "02:47:00"}, {"value": "1", "dateTime": "02:48:00"}, {"value": "1", "dateTime": "02:49:00"}, {"value": "1", "dateTime": "02:50:00"}, {"value": "1", "dateTime": "02:51:00"}, {"value": "1", "dateTime": "02:52:00"}, {"value": "1", "dateTime": "02:53:00"}, {"value": "1", "dateTime": "02:54:00"}, {"value": "1", "dateTime": "02:55:00"}, {"value": "1", "dateTime": "02:56:00"}, {"value": "1", "dateTime": "02:57:00"}, {"value": "1", "dateTime": "02:58:00"}, {"value": "1", "dateTime": "02:59:00"}, {"value": "1", "dateTime": "03:00:00"}, {"value": "1", "dateTime": "03:01:00"}, {"value": "1", "dateTime": "03:02:00"}, {"value": "1", "dateTime": "03:03:00"}, {"value": "1", "dateTime": "03:04:00"}, {"value": "1", "dateTime": "03:05:00"}, {"value": "1", "dateTime": "03:06:00"}, {"value": "1", "dateTime": "03:07:00"}, {"value": "1", "dateTime": "03:08:00"}, {"value": "1", "dateTime": "03:09:00"}, {"value": "2", "dateTime": "03:10:00"}, {"value": "2", "dateTime": "03:11:00"}, {"value": "2", "dateTime": "03:12:00"}, {"value": "1", "dateTime": "03:13:00"}, {"value": "1", "dateTime": "03:14:00"}, {"value": "1", "dateTime": "03:15:00"}, {"value": "1", "dateTime": "03:16:00"}, {"value": "1", "dateTime": "03:17:00"}, {"value": "1", "dateTime": "03:18:00"}, {"value": "1", "dateTime": "03:19:00"}, {"value": "1", "dateTime": "03:20:00"}, {"value": "1", "dateTime": "03:21:00"}, {"value": "1", "dateTime": "03:22:00"}, {"value": "1", "dateTime": "03:23:00"}, {"value": "1", "dateTime": "03:24:00"}, {"value": "1", "dateTime": "03:25:00"}, {"value": "1", "dateTime": "03:26:00"}, {"value": "1", "dateTime": "03:27:00"}, {"value": "1", "dateTime": "03:28:00"}, {"value": "1", "dateTime": "03:29:00"}, {"value": "1", "dateTime": "03:30:00"}, {"value": "1", "dateTime": "03:31:00"}, {"value": "1", "dateTime": "03:32:00"}, {"value": "1", "dateTime": "03:33:00"}, {"value": "1", "dateTime": "03:34:00"}, {"value": "1", "dateTime": "03:35:00"}, {"value": "1", "dateTime": "03:36:00"}, {"value": "1", "dateTime": "03:37:00"}, {"value": "1", "dateTime": "03:38:00"}, {"value": "1", "dateTime": "03:39:00"}, {"value": "1", "dateTime": "03:40:00"}, {"value": "1", "dateTime": "03:41:00"}, {"value": "1", "dateTime": "03:42:00"}, {"value": "1", "dateTime": "03:43:00"}, {"value": "1", "dateTime": "03:44:00"}, {"value": "1", "dateTime": "03:45:00"}, {"value": "1", "dateTime": "03:46:00"}, {"value": "1", "dateTime": "03:47:00"}, {"value": "1", "dateTime": "03:48:00"}, {"value": "1", "dateTime": "03:49:00"}, {"value": "2", "dateTime": "03:50:00"}, {"value": "1", "dateTime": "03:51:00"}, {"value": "1", "dateTime": "03:52:00"}, {"value": "1", "dateTime": "03:53:00"}, {"value": "1", "dateTime": "03:54:00"}, {"value": "1", "dateTime": "03:55:00"}, {"value": "1", "dateTime": "03:56:00"}, {"value": "1", "dateTime": "03:57:00"}, {"value": "1", "dateTime": "03:58:00"}, {"value": "1", "dateTime": "03:59:00"}, {"value": "1", "dateTime": "04:00:00"}, {"value": "1", "dateTime": "04:01:00"}, {"value": "1", "dateTime": "04:02:00"}, {"value": "1", "dateTime": "04:03:00"}, {"value": "1", "dateTime": "04:04:00"}, {"value": "1", "dateTime": "04:05:00"}, {"value": "1", "dateTime": "04:06:00"}, {"value": "1", "dateTime": "04:07:00"}, {"value": "1", "dateTime": "04:08:00"}, {"value": "1", "dateTime": "04:09:00"}, {"value": "1", "dateTime": "04:10:00"}, {"value": "1", "dateTime": "04:11:00"}, {"value": "1", "dateTime": "04:12:00"}, {"value": "1", "dateTime": "04:13:00"}, {"value": "1", "dateTime": "04:14:00"}, {"value": "1", "dateTime": "04:15:00"}, {"value": "1", "dateTime": "04:16:00"}, {"value": "1", "dateTime": "04:17:00"}, {"value": "1", "dateTime": "04:18:00"}, {"value": "1", "dateTime": "04:19:00"}, {"value": "1", "dateTime": "04:20:00"}, {"value": "1", "dateTime": "04:21:00"}, {"value": "1", "dateTime": "04:22:00"}, {"value": "1", "dateTime": "04:23:00"}, {"value": "1", "dateTime": "04:24:00"}, {"value": "1", "dateTime": "04:25:00"}, {"value": "1", "dateTime": "04:26:00"}, {"value": "1", "dateTime": "04:27:00"}, {"value": "1", "dateTime": "04:28:00"}, {"value": "1", "dateTime": "04:29:00"}, {"value": "1", "dateTime": "04:30:00"}, {"value": "1", "dateTime": "04:31:00"}, {"value": "1", "dateTime": "04:32:00"}, {"value": "1", "dateTime": "04:33:00"}, {"value": "1", "dateTime": "04:34:00"}, {"value": "1", "dateTime": "04:35:00"}, {"value": "1", "dateTime": "04:36:00"}, {"value": "1", "dateTime": "04:37:00"}, {"value": "1", "dateTime": "04:38:00"}, {"value": "1", "dateTime": "04:39:00"}, {"value": "1", "dateTime": "04:40:00"}, {"value": "1", "dateTime": "04:41:00"}, {"value": "1", "dateTime": "04:42:00"}, {"value": "1", "dateTime": "04:43:00"}, {"value": "1", "dateTime": "04:44:00"}, {"value": "1", "dateTime": "04:45:00"}, {"value": "1", "dateTime": "04:46:00"}, {"value": "1", "dateTime": "04:47:00"}, {"value": "1", "dateTime": "04:48:00"}, {"value": "1", "dateTime": "04:49:00"}, {"value": "1", "dateTime": "04:50:00"}, {"value": "1", "dateTime": "04:51:00"}, {"value": "1", "dateTime": "04:52:00"}, {"value": "1", "dateTime": "04:53:00"}, {"value": "1", "dateTime": "04:54:00"}, {"value": "1", "dateTime": "04:55:00"}, {"value": "1", "dateTime": "04:56:00"}, {"value": "1", "dateTime": "04:57:00"}, {"value": "1", "dateTime": "04:58:00"}, {"value": "1", "dateTime": "04:59:00"}, {"value": "1", "dateTime": "05:00:00"}, {"value": "1", "dateTime": "05:01:00"}, {"value": "1", "dateTime": "05:02:00"}, {"value": "1", "dateTime": "05:03:00"}, {"value": "1", "dateTime": "05:04:00"}, {"value": "1", "dateTime": "05:05:00"}, {"value": "1", "dateTime": "05:06:00"}, {"value": "1", "dateTime": "05:07:00"}, {"value": "1", "dateTime": "05:08:00"}, {"value": "1", "dateTime": "05:09:00"}, {"value": "1", "dateTime": "05:10:00"}, {"value": "1", "dateTime": "05:11:00"}, {"value": "1", "dateTime": "05:12:00"}, {"value": "1", "dateTime": "05:13:00"}, {"value": "1", "dateTime": "05:14:00"}, {"value": "1", "dateTime": "05:15:00"}, {"value": "1", "dateTime": "05:16:00"}, {"value": "1", "dateTime": "05:17:00"}, {"value": "1", "dateTime": "05:18:00"}, {"value": "1", "dateTime": "05:19:00"}, {"value": "1", "dateTime": "05:20:00"}, {"value": "1", "dateTime": "05:21:00"}, {"value": "1", "dateTime": "05:22:00"}, {"value": "1", "dateTime": "05:23:00"}, {"value": "1", "dateTime": "05:24:00"}, {"value": "1", "dateTime": "05:25:00"}, {"value": "1", "dateTime": "05:26:00"}, {"value": "1", "dateTime": "05:27:00"}, {"value": "1", "dateTime": "05:28:00"}, {"value": "1", "dateTime": "05:29:00"}, {"value": "1", "dateTime": "05:30:00"}, {"value": "1", "dateTime": "05:31:00"}, {"value": "1", "dateTime": "05:32:00"}, {"value": "1", "dateTime": "05:33:00"}, {"value": "1", "dateTime": "05:34:00"}, {"value": "1", "dateTime": "05:35:00"}, {"value": "1", "dateTime": "05:36:00"}, {"value": "1", "dateTime": "05:37:00"}, {"value": "1", "dateTime": "05:38:00"}, {"value": "1", "dateTime": "05:39:00"}, {"value": "1", "dateTime": "05:40:00"}, {"value": "1", "dateTime": "05:41:00"}, {"value": "1", "dateTime": "05:42:00"}, {"value": "1", "dateTime": "05:43:00"}, {"value": "1", "dateTime": "05:44:00"}, {"value": "1", "dateTime": "05:45:00"}, {"value": "1", "dateTime": "05:46:00"}, {"value": "1", "dateTime": "05:47:00"}, {"value": "1", "dateTime": "05:48:00"}, {"value": "1", "dateTime": "05:49:00"}, {"value": "1", "dateTime": "05:50:00"}, {"value": "1", "dateTime": "05:51:00"}, {"value": "1", "dateTime": "05:52:00"}, {"value": "1", "dateTime": "05:53:00"}, {"value": "1", "dateTime": "05:54:00"}, {"value": "1", "dateTime": "05:55:00"}, {"value": "1", "dateTime": "05:56:00"}, {"value": "1", "dateTime": "05:57:00"}, {"value": "1", "dateTime": "05:58:00"}, {"value": "1", "dateTime": "05:59:00"}, {"value": "1", "dateTime": "06:00:00"}, {"value": "1", "dateTime": "06:01:00"}, {"value": "1", "dateTime": "06:02:00"}, {"value": "1", "dateTime": "06:03:00"}, {"value": "1", "dateTime": "06:04:00"}, {"value": "1", "dateTime": "06:05:00"}, {"value": "1", "dateTime": "06:06:00"}, {"value": "1", "dateTime": "06:07:00"}, {"value": "1", "dateTime": "06:08:00"}, {"value": "1", "dateTime": "06:09:00"}, {"value": "1", "dateTime": "06:10:00"}, {"value": "1", "dateTime": "06:11:00"}, {"value": "1", "dateTime": "06:12:00"}, {"value": "1", "dateTime": "06:13:00"}, {"value": "1", "dateTime": "06:14:00"}, {"value": "1", "dateTime": "06:15:00"}, {"value": "1", "dateTime": "06:16:00"}, {"value": "1", "dateTime": "06:17:00"}, {"value": "1", "dateTime": "06:18:00"}, {"value": "1", "dateTime": "06:19:00"}, {"value": "1", "dateTime": "06:20:00"}, {"value": "1", "dateTime": "06:21:00"}, {"value": "1", "dateTime": "06:22:00"}, {"value": "1", "dateTime": "06:23:00"}, {"value": "1", "dateTime": "06:24:00"}, {"value": "1", "dateTime": "06:25:00"}, {"value": "1", "dateTime": "06:26:00"}, {"value": "1", "dateTime": "06:27:00"}, {"value": "1", "dateTime": "06:28:00"}, {"value": "1", "dateTime": "06:29:00"}, {"value": "1", "dateTime": "06:30:00"}, {"value": "1", "dateTime": "06:31:00"}, {"value": "1", "dateTime": "06:32:00"}, {"value": "1", "dateTime": "06:33:00"}, {"value": "1", "dateTime": "06:34:00"}, {"value": "1", "dateTime": "06:35:00"}, {"value": "1", "dateTime": "06:36:00"}, {"value": "1", "dateTime": "06:37:00"}, {"value": "1", "dateTime": "06:38:00"}, {"value": "1", "dateTime": "06:39:00"}, {"value": "1", "dateTime": "06:40:00"}, {"value": "2", "dateTime": "06:41:00"}, {"value": "1", "dateTime": "06:42:00"}, {"value": "1", "dateTime": "06:43:00"}, {"value": "1", "dateTime": "06:44:00"}, {"value": "1", "dateTime": "06:45:00"}, {"value": "1", "dateTime": "06:46:00"}, {"value": "1", "dateTime": "06:47:00"}, {"value": "1", "dateTime": "06:48:00"}, {"value": "1", "dateTime": "06:49:00"}, {"value": "1", "dateTime": "06:50:00"}, {"value": "1", "dateTime": "06:51:00"}, {"value": "1", "dateTime": "06:52:00"}, {"value": "1", "dateTime": "06:53:00"}, {"value": "1", "dateTime": "06:54:00"}, {"value": "1", "dateTime": "06:55:00"}, {"value": "1", "dateTime": "06:56:00"}, {"value": "1", "dateTime": "06:57:00"}, {"value": "1", "dateTime": "06:58:00"}, {"value": "1", "dateTime": "06:59:00"}, {"value": "1", "dateTime": "07:00:00"}, {"value": "1", "dateTime": "07:01:00"}, {"value": "1", "dateTime": "07:02:00"}, {"value": "1", "dateTime": "07:03:00"}, {"value": "1", "dateTime": "07:04:00"}, {"value": "1", "dateTime": "07:05:00"}, {"value": "1", "dateTime": "07:06:00"}, {"value": "1", "dateTime": "07:07:00"}, {"value": "1", "dateTime": "07:08:00"}, {"value": "1", "dateTime": "07:09:00"}, {"value": "1", "dateTime": "07:10:00"}, {"value": "1", "dateTime": "07:11:00"}, {"value": "1", "dateTime": "07:12:00"}, {"value": "1", "dateTime": "07:13:00"}, {"value": "1", "dateTime": "07:14:00"}, {"value": "1", "dateTime": "07:15:00"}, {"value": "1", "dateTime": "07:16:00"}, {"value": "1", "dateTime": "07:17:00"}, {"value": "1", "dateTime": "07:18:00"}, {"value": "1", "dateTime": "07:19:00"}, {"value": "1", "dateTime": "07:20:00"}, {"value": "1", "dateTime": "07:21:00"}, {"value": "1", "dateTime": "07:22:00"}, {"value": "1", "dateTime": "07:23:00"}, {"value": "1", "dateTime": "07:24:00"}, {"value": "1", "dateTime": "07:25:00"}, {"value": "1", "dateTime": "07:26:00"}, {"value": "1", "dateTime": "07:27:00"}, {"value": "1", "dateTime": "07:28:00"}, {"value": "1", "dateTime": "07:29:00"}, {"value": "1", "dateTime": "07:30:00"}, {"value": "1", "dateTime": "07:31:00"}, {"value": "1", "dateTime": "07:32:00"}, {"value": "1", "dateTime": "07:33:00"}, {"value": "1", "dateTime": "07:34:00"}, {"value": "1", "dateTime": "07:35:00"}, {"value": "1", "dateTime": "07:36:00"}, {"value": "1", "dateTime": "07:37:00"}, {"value": "1", "dateTime": "07:38:00"}, {"value": "1", "dateTime": "07:39:00"}, {"value": "1", "dateTime": "07:40:00"}, {"value": "1", "dateTime": "07:41:00"}, {"value": "1", "dateTime": "07:42:00"}, {"value": "1", "dateTime": "07:43:00"}, {"value": "1", "dateTime": "07:44:00"}, {"value": "1", "dateTime": "07:45:00"}, {"value": "1", "dateTime": "07:46:00"}, {"value": "1", "dateTime": "07:47:00"}, {"value": "1", "dateTime": "07:48:00"}, {"value": "1", "dateTime": "07:49:00"}, {"value": "2", "dateTime": "07:50:00"}, {"value": "2", "dateTime": "07:51:00"}, {"value": "2", "dateTime": "07:52:00"}, {"value": "3", "dateTime": "07:53:00"}, {"value": "3", "dateTime": "07:54:00"}, {"value": "3", "dateTime": "07:55:00"}, {"value": "3", "dateTime": "07:56:00"}, {"value": "3", "dateTime": "07:57:00"}, {"value": "3", "dateTime": "07:58:00"}, {"value": "3", "dateTime": "07:59:00"}, {"value": "3", "dateTime": "08:00:00"}, {"value": "3", "dateTime": "08:01:00"}, {"value": "3", "dateTime": "08:02:00"}, {"value": "2", "dateTime": "08:03:00"}, {"value": "1", "dateTime": "08:04:00"}, {"value": "1", "dateTime": "08:05:00"}, {"value": "1", "dateTime": "08:06:00"}, {"value": "1", "dateTime": "08:07:00"}, {"value": "1", "dateTime": "08:08:00"}, {"value": "1", "dateTime": "08:09:00"}, {"value": "1", "dateTime": "08:10:00"}, {"value": "1", "dateTime": "08:11:00"}, {"value": "1", "dateTime": "08:12:00"}, {"value": "1", "dateTime": "08:13:00"}, {"value": "1", "dateTime": "08:14:00"}, {"value": "1", "dateTime": "08:15:00"}, {"value": "1", "dateTime": "08:16:00"}, {"value": "1", "dateTime": "08:17:00"}, {"value": "1", "dateTime": "08:18:00"}, {"value": "1", "dateTime": "08:19:00"}, {"value": "1", "dateTime": "08:20:00"}, {"value": "1", "dateTime": "08:21:00"}, {"value": "1", "dateTime": "08:22:00"}, {"value": "1", "dateTime": "08:23:00"}, {"value": "1", "dateTime": "08:24:00"}, {"value": "1", "dateTime": "08:25:00"}, {"value": "1", "dateTime": "08:26:00"}, {"value": "1", "dateTime": "08:27:00"}, {"value": "1", "dateTime": "08:28:00"}, {"value": "1", "dateTime": "08:29:00"}, {"value": "1", "dateTime": "08:30:00"}, {"value": "1", "dateTime": "08:31:00"}, {"value": "1", "dateTime": "08:32:00"}, {"value": "1", "dateTime": "08:33:00"}, {"value": "1", "dateTime": "08:34:00"}, {"value": "1", "dateTime": "08:35:00"}, {"value": "1", "dateTime": "08:36:00"}, {"value": "1", "dateTime": "08:37:00"}, {"value": "1", "dateTime": "08:38:00"}, {"value": "1", "dateTime": "08:39:00"}, {"value": "1", "dateTime": "08:40:00"}, {"value": "1", "dateTime": "08:41:00"}, {"value": "1", "dateTime": "08:42:00"}, {"value": "1", "dateTime": "08:43:00"}, {"value": "1", "dateTime": "08:44:00"}, {"value": "1", "dateTime": "08:45:00"}, {"value": "1", "dateTime": "08:46:00"}, {"value": "1", "dateTime": "08:47:00"}, {"value": "1", "dateTime": "08:48:00"}, {"value": "1", "dateTime": "08:49:00"}, {"value": "1", "dateTime": "08:50:00"}, {"value": "1", "dateTime": "08:51:00"}, {"value": "1", "dateTime": "08:52:00"}, {"value": "1", "dateTime": "08:53:00"}, {"value": "1", "dateTime": "08:54:00"}, {"value": "1", "dateTime": "08:55:00"}, {"value": "1", "dateTime": "08:56:00"}, {"value": "1", "dateTime": "08:57:00"}, {"value": "1", "dateTime": "08:58:00"}, {"value": "1", "dateTime": "08:59:00"}, {"value": "1", "dateTime": "09:00:00"}, {"value": "1", "dateTime": "09:01:00"}, {"value": "1", "dateTime": "09:02:00"}, {"value": "1", "dateTime": "09:03:00"}, {"value": "1", "dateTime": "09:04:00"}, {"value": "1", "dateTime": "09:05:00"}, {"value": "1", "dateTime": "09:06:00"}, {"value": "1", "dateTime": "09:07:00"}, {"value": "1", "dateTime": "09:08:00"}, {"value": "1", "dateTime": "09:09:00"}, {"value": "1", "dateTime": "09:10:00"}, {"value": "1", "dateTime": "09:11:00"}, {"value": "1", "dateTime": "09:12:00"}, {"value": "1", "dateTime": "09:13:00"}, {"value": "1", "dateTime": "09:14:00"}, {"value": "1", "dateTime": "09:15:00"}, {"value": "1", "dateTime": "09:16:00"}, {"value": "1", "dateTime": "09:17:00"}, {"value": "1", "dateTime": "09:18:00"}, {"value": "1", "dateTime": "09:19:00"}, {"value": "1", "dateTime": "09:20:00"}, {"value": "1", "dateTime": "09:21:00"}, {"value": "1", "dateTime": "09:22:00"}, {"value": "1", "dateTime": "09:23:00"}, {"value": "1", "dateTime": "09:24:00"}, {"value": "1", "dateTime": "09:25:00"}, {"value": "1", "dateTime": "09:26:00"}, {"value": "1", "dateTime": "09:27:00"}, {"value": "1", "dateTime": "09:28:00"}, {"value": "1", "dateTime": "09:29:00"}, {"value": "1", "dateTime": "09:30:00"}, {"value": "1", "dateTime": "09:31:00"}, {"value": "1", "dateTime": "09:32:00"}, {"value": "1", "dateTime": "09:33:00"}, {"value": "1", "dateTime": "09:34:00"}, {"value": "1", "dateTime": "09:35:00"}, {"value": "1", "dateTime": "09:36:00"}, {"value": "1", "dateTime": "09:37:00"}, {"value": "1", "dateTime": "09:38:00"}, {"value": "1", "dateTime": "09:39:00"}, {"value": "1", "dateTime": "09:40:00"}, {"value": "1", "dateTime": "09:41:00"}, {"value": "1", "dateTime": "09:42:00"}, {"value": "1", "dateTime": "09:43:00"}, {"value": "1", "dateTime": "09:44:00"}, {"value": "1", "dateTime": "09:45:00"}, {"value": "1", "dateTime": "09:46:00"}], "endTime": "2017-12-23T09:47:30.000", "awakeCount": 2, "minutesAfterWakeup": 0}], "summary": {"totalTimeInBed": 547, "stages": {"light": 334, "wake": 50, "deep": 57, "rem": 106}, "totalMinutesAsleep": 524, "totalSleepRecords": 1}}


Let's parse this guy.


```python
list(parsed_json['sleep'][0].keys())
```




    [u'logId',
     u'dateOfSleep',
     u'minutesToFallAsleep',
     u'awakeningsCount',
     u'minutesAwake',
     u'timeInBed',
     u'minutesAsleep',
     u'awakeDuration',
     u'efficiency',
     u'isMainSleep',
     u'startTime',
     u'restlessCount',
     u'duration',
     u'restlessDuration',
     u'minuteData',
     u'endTime',
     u'awakeCount',
     u'minutesAfterWakeup']



Now we are ready to read in all the sleep files. I then concatonated the files using the .append command. Finally, I convert the data frame into a pandas data frame using the pd.DataFrame command. Boom.


```python
sleepy = []
for date in dates:
    fname = 'sleep/sleep' + date.strftime('%Y-%m-%d') + '.json'
    with open(fname) as f:
        date_data_sleep = json.loads(f.read())
        datsleep = pd.read_json(date_data_sleep, typ='series')
        sleepy.append(datsleep['summary']['totalTimeInBed'])

sleepdf = pd.DataFrame(sleepy,index = dates) #use the date as the column row names
sleepdf.columns = ['hours'] #rename column
sleepdf['hours'] = sleepdf['hours']/60 # Turn minutes to hours
```

Time to see the beautiful new df!


```python
sleepdf.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hours</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-23</th>
      <td>9.116667</td>
    </tr>
    <tr>
      <th>2017-12-24</th>
      <td>9.250000</td>
    </tr>
    <tr>
      <th>2017-12-25</th>
      <td>8.683333</td>
    </tr>
    <tr>
      <th>2017-12-26</th>
      <td>9.966667</td>
    </tr>
    <tr>
      <th>2017-12-27</th>
      <td>7.500000</td>
    </tr>
  </tbody>
</table>
</div>



And again, check for outliers just in case. You never know. But, it all looks good.


```python
sleepdf.describe()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hours</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>34.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>8.486765</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.420336</td>
    </tr>
    <tr>
      <th>min</th>
      <td>4.966667</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>7.587500</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>8.383333</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>9.312500</td>
    </tr>
    <tr>
      <th>max</th>
      <td>12.066667</td>
    </tr>
  </tbody>
</table>
</div>



Plot it out in a nice simple graph (ya basic). I should probably stick to a better sleep schedule.


```python
sleepdf['hours'].plot(color = "mediumseagreen")
plt.title('Hours of Sleep (12.23.17-1.25.18)')
plt.ylabel('Hours')
plt.show()
```


![png](output_32_0.png)


Let's plot the sleep data another way...how about a distribution?

By the way, what do you call a haunted distrubution?  A paranormal distribution. Ha! Sorry guys. 


```python
sns.distplot(sleepdf['hours'], color="mediumseagreen")
plt.title("My Sleep Distribution")
plt.xlabel("Hours")
plt.show()
```


![png](output_34_0.png)


Add column and labels for day of week, just as before.


```python
sleepdf["day_of_week"] = sleepdf.index.weekday
days = {0: "Monday", 1: "Tuesday", 2: "Wednesday", 3: "Thursday", 4: "Friday", 
        5: "Saturday", 6: "Sunday"}
sleepdf["day_name"] = sleepdf["day_of_week"].apply(lambda x: days[x])
sleepdf.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hours</th>
      <th>day_of_week</th>
      <th>day_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-23</th>
      <td>9.116667</td>
      <td>5</td>
      <td>Saturday</td>
    </tr>
    <tr>
      <th>2017-12-24</th>
      <td>9.250000</td>
      <td>6</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>2017-12-25</th>
      <td>8.683333</td>
      <td>0</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>2017-12-26</th>
      <td>9.966667</td>
      <td>1</td>
      <td>Tuesday</td>
    </tr>
    <tr>
      <th>2017-12-27</th>
      <td>7.500000</td>
      <td>2</td>
      <td>Wednesday</td>
    </tr>
  </tbody>
</table>
</div>



For kicks, we should probably check and officially see if we have an even amount of data per day (in case I forgot to put it on one night or the battery died). 

This is important if we want to trust the accuracy of our analyses related to days of the week! According to the plot, it's all pretty even, except for Friday. This is because I imported data before the 5th Friday.


```python
# Make a Bar Plot 
sns.countplot(x='day_name', data=sleepdf, palette = "Set2")
plt.xticks(rotation=45)
plt.xlabel("")
plt.show()
```


![png](output_39_0.png)


How does my sleep vary from day to day? Looks like Sunday is really variable.


```python
sns.swarmplot(x='day_name', y='hours', data=sleepdf,  palette = "Set2")
plt.xticks(rotation=45)
plt.title("Sleep Distribution Over the Week")
plt.ylabel("Hours")
plt.xlabel("")
plt.show()
```


![png](output_41_0.png)


For kicks, we should probably check and officially see if we have an even amount of data per day (in case I forgot to put it on one night or the battery died). This is important if we want to trust the accuracy of our analyses related to days of the week!

Another view- violin style!


```python
sns.violinplot(x='day_name',
               y='hours', 
               data=sleepdf, 
               inner=None, palette = 'Set2' ) # Remove the bars inside the violins

## plot the dots we see above over the violin plot.
sns.swarmplot(x='day_name',
               y='hours', 
              data=sleepdf, 
              color='k', 
              alpha=0.7)
 
# Set title with matplotlib
plt.title('Sleep by Day')
plt.xticks(rotation=45)
plt.show()
```


![png](output_44_0.png)


Okay, last sleep plot visual, for now. The graph below is more typical of something I would encounter in academia, and it also shows the standard errors of my sleep during each day. 


```python
sns.boxplot(x = 'day_name', y = 'hours', data = sleepdf, palette = 'Set2')
plt.title('Hours of Sleep by Day')
plt.xticks(rotation=45)
plt.xlabel("") 
plt.ylabel("")
plt.show()
```


![png](output_46_0.png)


## Step data 

Read it in. And check structure of .json file, it's a mystery. 


```python
with open('steps/step2018-01-13.json') as f:
    step_data = json.loads(f.read())
stepsdata = pd.read_json(step_data, typ='series')
stepsdata
```




    activities-steps    [{u'value': u'12721', u'dateTime': u'2018-01-1...
    dtype: object




```python
parsed_json = json.loads(step_data)
#print(json.dumps(parsed_json))

```

    {"activities-steps": [{"value": "12721", "dateTime": "2018-01-13"}, {"value": "9687", "dateTime": "2018-01-14"}, {"value": "12920", "dateTime": "2018-01-15"}, {"value": "5894", "dateTime": "2018-01-16"}, {"value": "8710", "dateTime": "2018-01-17"}, {"value": "14462", "dateTime": "2018-01-18"}, {"value": "6660", "dateTime": "2018-01-19"}, {"value": "10138", "dateTime": "2018-01-20"}, {"value": "8497", "dateTime": "2018-01-21"}, {"value": "10437", "dateTime": "2018-01-22"}, {"value": "9005", "dateTime": "2018-01-23"}, {"value": "6531", "dateTime": "2018-01-24"}, {"value": "10663", "dateTime": "2018-01-25"}]}


Get higher level key name(s).


```python
list(parsed_json.keys())
```




    [u'activities-steps']




```python
dates = pd.date_range('2017-12-23', '2018-01-25')
steppers = []
for date in dates:
    fname = 'steps/step' + date.strftime('%Y-%m-%d') + '.json'
    with open(fname) as f:
        date_data = json.loads(f.read())
        
        stepsdata = pd.read_json(date_data, typ='series')
        steppers.append(stepsdata['activities-steps'][0]) # the zero indexes the first value

stepsdf = pd.DataFrame(steppers,index = dates) # use date as row index
stepsdf.columns = ['date','steps'] # rename columns
```


```python
stepsdf.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>steps</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-23</th>
      <td>2017-12-23</td>
      <td>4238</td>
    </tr>
    <tr>
      <th>2017-12-24</th>
      <td>2017-12-24</td>
      <td>10776</td>
    </tr>
    <tr>
      <th>2017-12-25</th>
      <td>2017-12-25</td>
      <td>4928</td>
    </tr>
    <tr>
      <th>2017-12-26</th>
      <td>2017-12-26</td>
      <td>9468</td>
    </tr>
    <tr>
      <th>2017-12-27</th>
      <td>2017-12-27</td>
      <td>7046</td>
    </tr>
  </tbody>
</table>
</div>



Add column for the day of week, just as before. 


```python
stepsdf["day_of_week"] = stepsdf.index.weekday
stepsdf["day_name"] = stepsdf["day_of_week"].apply(lambda x: days[x])
#Have a look!
stepsdf.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>steps</th>
      <th>day_of_week</th>
      <th>day_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-23</th>
      <td>2017-12-23</td>
      <td>4238</td>
      <td>5</td>
      <td>Saturday</td>
    </tr>
    <tr>
      <th>2017-12-24</th>
      <td>2017-12-24</td>
      <td>10776</td>
      <td>6</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>2017-12-25</th>
      <td>2017-12-25</td>
      <td>4928</td>
      <td>0</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>2017-12-26</th>
      <td>2017-12-26</td>
      <td>9468</td>
      <td>1</td>
      <td>Tuesday</td>
    </tr>
    <tr>
      <th>2017-12-27</th>
      <td>2017-12-27</td>
      <td>7046</td>
      <td>2</td>
      <td>Wednesday</td>
    </tr>
  </tbody>
</table>
</div>



To perform any analyses or plot data, we need to convert steps column into a numeric type. Otherwise we get errors. We don't like errors.


```python
stepsdf['steps'] = pd.to_numeric(stepsdf['steps'])
stepsdf.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 34 entries, 2017-12-23 to 2018-01-25
    Freq: D
    Data columns (total 4 columns):
    date           34 non-null object
    steps          34 non-null int64
    day_of_week    34 non-null int64
    day_name       34 non-null object
    dtypes: int64(2), object(2)
    memory usage: 1.3+ KB


Now it's numeric! Anything is pos-iiii-ble!

Anywho... let's see my step distribution.


```python
sns.set_style("ticks")
sns.distplot(stepsdf['steps'], color = 'steelblue')
plt.title("Step Distribution")
plt.xlabel("Steps") 
plt.show()
```


![png](output_60_0.png)


Violin time.

 I shall call it: "Friday: run or couch?"


```python
sns.violinplot(x='day_name',
               y='steps', 
               data=stepsdf, 
               inner=None, palette="Set3" ) # Remove the bars inside the violins
            
## Overlay dots onto violins.
sns.swarmplot(x='day_name',
               y='steps', 
              data=stepsdf, 
              color='k',  #black dots
              alpha = 0.5)
plt.title('Steps by Day')
plt.xticks(rotation=45)
plt.xlabel("") 
plt.ylabel("")
plt.show()
```


![png](output_63_0.png)


Boxplot - bam.


```python
sns.boxplot(x = 'day_name', y = 'steps', data = stepsdf, palette="Set3")
plt.title('Steps by Day')
plt.xticks(rotation=45)
plt.xlabel("") 
plt.ylabel("")
plt.show()
```


![png](output_65_0.png)



```python
## Put hours into the right format
import statsmodels.api as sm
import statsmodels.formula.api as smf
from pandas.core import datetools
stepsdf['hours'] = sleepdf['hours'].values
```

## 3. Analyses (!!)

## Linear regression models

### Question 1: Am I more active on days after I get more sleep? 

I am operationalizing active to mean more steps per day.

In the model below, I am predicting my steps from the hours of sleep I got the night before. I am using an ordinary least squares regression model. Below is the code for the model, fitting the model, and displaying the results. 

First, create variable for the previous night's sleep and align it properly.


```python
stepsdf["hours_prev"] = stepsdf.shift(1).hours
stepsdf.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>steps</th>
      <th>day_of_week</th>
      <th>day_name</th>
      <th>hours</th>
      <th>hours_prev</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-23</th>
      <td>2017-12-23</td>
      <td>4238</td>
      <td>5</td>
      <td>Saturday</td>
      <td>9.116667</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2017-12-24</th>
      <td>2017-12-24</td>
      <td>10776</td>
      <td>6</td>
      <td>Sunday</td>
      <td>9.250000</td>
      <td>9.116667</td>
    </tr>
    <tr>
      <th>2017-12-25</th>
      <td>2017-12-25</td>
      <td>4928</td>
      <td>0</td>
      <td>Monday</td>
      <td>8.683333</td>
      <td>9.250000</td>
    </tr>
    <tr>
      <th>2017-12-26</th>
      <td>2017-12-26</td>
      <td>9468</td>
      <td>1</td>
      <td>Tuesday</td>
      <td>9.966667</td>
      <td>8.683333</td>
    </tr>
    <tr>
      <th>2017-12-27</th>
      <td>2017-12-27</td>
      <td>7046</td>
      <td>2</td>
      <td>Wednesday</td>
      <td>7.500000</td>
      <td>9.966667</td>
    </tr>
  </tbody>
</table>
</div>




```python
mod1 = smf.ols(formula = "steps ~ hours_prev", data = stepsdf).fit()
mod1.summary().tables[1]
```




<table class="simpletable">
<tr>
       <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>  <td> 7624.5193</td> <td> 3320.426</td> <td>    2.296</td> <td> 0.029</td> <td>  852.465</td> <td> 1.44e+04</td>
</tr>
<tr>
  <th>hours_prev</th> <td>  130.7044</td> <td>  384.278</td> <td>    0.340</td> <td> 0.736</td> <td> -653.036</td> <td>  914.445</td>
</tr>
</table>



## Results:

So, we can see from the regression table that how many hours I slept the previous night ('hours_prev') does not predict the amount of steps I take the next day. This might be for a variety of reasons. First, I don't have a lot of data yet! I have only used my Fitbit for a month. Second, I am a graduate student, and my sleep is incredibly variable. Third- I love to be active! Even if I am tired, a run usually makes me feel better. Long story short is, we need more data!


```python
sns.regplot(x = stepsdf.steps, y = stepsdf.hours_prev, color = 'steelblue')
plt.xlabel("Daily Steps")
plt.ylabel("Hours of Sleep")
plt.title("Does Sleep Amount Predict Activity Level?")
plt.show()
```


![png](output_72_0.png)


That flat line shows that there is no linear relationship- it's flat as a pancake, which sounds amazing right now.

## Linear regression model  part 2

### Question 2: Do I sleep need more sleep the night after I don't get a lot of sleep? In other words, am I building up a sleep deficit?

In the model below, I am predicting the difference in amount of sleep I get from the hours of sleep I got the night before. I am using an ordinary least squares regression model. Below is the code for the model, fitting the model, and displaying the results. 

But first, we need to create a delta sleep variable to capture the difference in amount of sleep I got from the night before. 


```python
stepsdf["hours_diff"] = stepsdf.hours - stepsdf.hours_prev
stepsdf.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>steps</th>
      <th>day_of_week</th>
      <th>day_name</th>
      <th>hours</th>
      <th>hours_prev</th>
      <th>hours_diff</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-23</th>
      <td>2017-12-23</td>
      <td>4238</td>
      <td>5</td>
      <td>Saturday</td>
      <td>9.116667</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2017-12-24</th>
      <td>2017-12-24</td>
      <td>10776</td>
      <td>6</td>
      <td>Sunday</td>
      <td>9.250000</td>
      <td>9.116667</td>
      <td>0.133333</td>
    </tr>
    <tr>
      <th>2017-12-25</th>
      <td>2017-12-25</td>
      <td>4928</td>
      <td>0</td>
      <td>Monday</td>
      <td>8.683333</td>
      <td>9.250000</td>
      <td>-0.566667</td>
    </tr>
    <tr>
      <th>2017-12-26</th>
      <td>2017-12-26</td>
      <td>9468</td>
      <td>1</td>
      <td>Tuesday</td>
      <td>9.966667</td>
      <td>8.683333</td>
      <td>1.283333</td>
    </tr>
    <tr>
      <th>2017-12-27</th>
      <td>2017-12-27</td>
      <td>7046</td>
      <td>2</td>
      <td>Wednesday</td>
      <td>7.500000</td>
      <td>9.966667</td>
      <td>-2.466667</td>
    </tr>
  </tbody>
</table>
</div>




```python
mod2 = smf.ols(formula = 'hours_diff ~ hours_prev', data = stepsdf).fit()
mod2.summary().tables[1]
```




<table class="simpletable">
<tr>
       <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>  <td>    9.4319</td> <td>    1.558</td> <td>    6.054</td> <td> 0.000</td> <td>    6.254</td> <td>   12.610</td>
</tr>
<tr>
  <th>hours_prev</th> <td>   -1.1131</td> <td>    0.180</td> <td>   -6.173</td> <td> 0.000</td> <td>   -1.481</td> <td>   -0.745</td>
</tr>
</table>



## Results:

The regression table shows that how many hours I slept the previous night ('hours_prev') negatively predicts the difference in amount of sleep I get the next day. Put more simply, this means that if I get a good night's sleep, the next night, I don't need as much sleep. Conversely, if I don't get a lot of sleep, the next night, I make up for it by sleeping longer.

To make this finding easier to digest, let us visualize the relationship!


```python
# Plot how much more I slept each night vs. amount slept night before
sns.regplot(x = stepsdf.hours_prev, y = stepsdf.hours_diff, color = "mediumseagreen")
plt.title("Sleep Deficit?")
plt.ylabel("Difference in Hours")
plt.xlabel("Hours of Sleep")
plt.show()
```


![png](output_78_0.png)


Poof! Here is that negtative linear relationship we saw in our regression table.

## 4. Next steps

In sum, we have imported data using Fitbit's API, worked with .json data, visualized our data, and we ran two linear regressions. We found that my activity level is dependent from how much sleep I get and that I built up a sleep deficit over time. Phew!! 

What I would like to do next is collect more data! I would also like to create some interactive visuals and run some more sophisticated models on my data. Finally, I would like to explore my heart rate more. The closer I get to defending my dissertation, the higher it seems to get. Should be fun to test if that is statistically significant! :) 

### If you have any suggestions on how I can improve, sources that may help improve my code, or comments/questions, please let me know! I am using this space to share and learn. 

Thanks!!