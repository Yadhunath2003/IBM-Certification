# ETL Weather Forecast Pipeline using Linux Commands

We will use the weather data package provided by the open-source project wttr.in, a web service that provides weather forecast information in a simple text-based format.

## Objectives of this Project

- Download raw weather data.
- Extract relevant data from the raw data.
- Transform the data as required.
- Load the data into a log file in a tabular format.
- Schedule the entire process to run automatically at a set time daily.

We will extract and store the following data every day at noon, local time, for Casablanca, Morocco:

- The actual temperature (in degrees Celsius)
- The forecasted temperature (in degrees Celsius) for the following day at noon

## Initializing Weather Report Log File:

```sh 

touch rx_poc.log

echo -e "year\tmonth\tday\tobs_temp\tfc_temp">rx_poc.log
```

## Download the raw weather data

```sh
touch rx_poc.sh

chmod u+x rx_poc.sh

ls -l rx_poc.sh #to make sure the access is assigned
```

## Extract and Load the required data in the shell file.

### rx_poc.sh
```sh
#! /bin/bash
 
#Assign city name as Casablanca
city=Casablanca

#Obtain the weather report for Casablanca
curl -s wttr.in/$city?T --output weather_report

#To extract Current Temperature
obs_temp=$(curl -s wttr.in/$city?T | grep -m 1 '°.' | grep -Eo -e '-?[[:digit:]].*')
echo "The current Temperature of $city: $obs_temp"

# To extract the forecasted temperature for noon tomorrow
fc_temp=$(curl -s wttr.in/$city?T | head -23 | tail -1 | grep '°.' | cut -d 'C' -f2 | grep -Eo -e '-?[[:digit:]].*')
echo "The forecasted temperature for noon tomorrow for $city : $fc_temp C"

# Assign the timezone for Casablanca, Morocco
TZ='Morocco/Casablanca'


# Use command substitution to store the current day, month, and year in corresponding shell variables:
day=$(TZ='Morocco/Casablanca' date -u +%d)
month=$(TZ='Morocco/Casablanca' date +%m)
year=$(TZ='Morocco/Casablanca' date +%Y)


# Log the weather
record=$(echo -e "$year\t$month\t$day\t$obs_temp\t$fc_temp")
echo $record>>rx_poc.log
```

## Scheduling the bash to run every day at Noon Local Time

First we need to determine the time difference between Casablanca and our Local time. We do so by running the following commands.

```sh
date #to give out our local time.
date -u #time at Casablanca
```

From this we get to know that we are 6 hours apart from Casablanca, thus we should be recording the weather forecast at 6:00AM local time to match the weather forecast at noon in Casablaca.

Now we create a Cronjob to run this script, and write the following command in the crontab file.

```sh
crontab -e
0 6 * * * /home/project/rx_poc.sh
```

## Create a script to report historical forecasting accuracy

To start, we create a tab-delimited file named `historical_fc_accuracy.tsv`.

```sh
echo -e "year\tmonth\tday\tobs_temp\tfc_temp\taccuracy\taccuracy_range" > historical_fc_accuracy.tsv
```

We also create an executable Bash script called `fc_accuracy.sh` to calculate the accuracy of the today's forecast compared to yesterday's forecast.

We will first change the permissions of the .sh file to make it executable.
```sh
touch fc_accuracy.sh

chmod u+x fc_accuracy.sh

ls -l fc_accuracy.sh #to make sure the access is assigned
```

### fc_accuracy.sh
```sh
#! /bin/bash

# Extract the forecasted and observed temperatures for yesterday and store them in variables.
yesterday_fc=$(tail -2 rx_poc.log | head -1 | cut -d " " -f5)
#Extracting the forcasted and observed temperatures for today and store them in variable.
today_temp=$(tail -1 rx_poc.log | cut -d " " -f4)
#Calculate the forecast accuracy.
accuracy=$((yesterday_fc - today_temp))

echo "Accuracy is $accuracy"

#Assigning a label to each forecast based on its accuracy.
if [ -1 -le $accuracy ] && [ $accuracy -le 1 ]
then
           accuracy_range=excellent
elif [ -2 -le $accuracy ] && [ $accuracy -le 2 ]
   then
               accuracy_range=good
       elif [ -3 -le $accuracy ] && [ $accuracy -le 3 ]
       then
                   accuracy_range=fair
           else
                       accuracy_range=poor
fi

echo "Forecast accuracy range is $accuracy_range"

#Appending a record to the historical forecast accuracy file.
row=$(tail -1 rx_poc.log)
year=$( echo $row | cut -d " " -f1)
month=$( echo $row | cut -d " " -f2)
day=$( echo $row | cut -d " " -f3)
echo -e "$year\t$month\t$day\t$today_temp\t$yesterday_fc\t$accuracy\t$accuracy_range" >> historical_fc_accuracy.tsv
```

## Create a script to report weekly statistics of historical forecasting accuracy

We will download a synthetic historical forecasting accuracy report and calculate some basic statistics based on the latest week of data in the `weekly_stats.sh` file.

First we download the synthetic hostorical forecasting accuracy data set, create the .sh, and modify it's permissions.

```sh
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-LX0117EN-Coursera/labs/synthetic_historical_fc_accuracy.tsv

touch weekly_stats.sh

chmod u+x weekly_stats.sh

ls -l weekly_stats.sh #to make sure the access is assigned
```

### weekly_stats.sh
```sh
#!/bin/bash

echo $(tail -7 synthetic_historical_fc_accuracy.tsv  | cut -f6) > scratch.txt

week_fc=($(echo $(cat scratch.txt)))

# validate result:
for i in {0..6}; do
    echo ${week_fc[$i]}
done

for i in {0..6}; do
  if [[ ${week_fc[$i]} < 0 ]]
  then
    week_fc[$i]=$(((-1)*week_fc[$i]))
  fi
  # validate result:
  echo ${week_fc[$i]}
done

#Display the min and max absolute forecasting errors for the week.
minimum=${week_fc[1]}
maximum=${week_fc[1]}
for item in ${week_fc[@]}; do
   if [[ $minimum > $item ]]
   then
     minimum=$item
   fi
   if [[ $maximum < $item ]]
   then
     maximum=$item
   fi
done

echo "minimum ebsolute error = $minimum"
echo "maximum absolute error = $maximum"
```

This concludes the project and we can check the results by running the following commands and these were the results that we obtained.

```./rx_poc.sh```

```sh

./fc_accuracy.sh

#result:
accuracy is 0
Forecast accuracy is excellent

```

```sh
./weekly_stats.sh

#result:
-5  
-1   
-2
4
-2
0
1
5
1
2
4
2
0
1
minimum ebsolute error = 0
maximum absolute error = 5
```


