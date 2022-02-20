# Surfs_Up

## Overview of Project

### Purpose

The purpose of this analysis was to read in data from an sqlite file which contained daily measurements of temperature and precipitations to help determine if a Surf & Ice Cream Shop in Oahu Hawaii would be sustainable year round. Through the use of pandas and sqlalchemy we hope to gain some insight on the difference in climate during June & December

## Results

After importing all of our dependencies we set up our engine to read in the sqlite file and reflect the database into a new model, along with the tables. In addition to our normal dependencies, I chose to add the following ```from sqlalchemy import MetaData, Table``` so I could take a look at what column keys were available for inspection through queries. When it comes to selecting the months of June & December from our database, we will need to use regular expressions to efficiently select the data we are interested in. Our regex expression is as follows:

```regex = r'\d{4}-06-\d{2}'``` for the month of June

```regex = r'\d{4}-12-\d{2}'``` for the month of December

Next, we will make a query request: ```results = session.query(Measurement.tobs).filter(Measurement.date.op('regexp')(regex).all()```. This request accesses the temperature measurements (Measurement.tobs) and filters them by only selecting dates that match our respective regular expressions. After putting our temperature data into DataFrames we can run a descriptive statistic on each using ```df.describe()```


![june_temps_df](https://github.com/brand0j/Surfs_Up/blob/main/Resources/june_temps_df.PNG)
![december_temps_df](https://github.com/brand0j/Surfs_Up/blob/main/Resources/december_temps_df.PNG)

## Summary

From the tables presented above we can draw a few conclusions. The mean temperature in June is 74.94°F (with std of ~3.26°) while the mean temperature in December is 71.04°F (with std of ~3.75°). Another thing to note is the drastic difference in our lowest temperature during each month (June: 64° & December: 56°) while the maximum temperatures are relatively close with one another. To justify the Surf & Ice Cream shop remaining open during December, we should consider the IQR for June to help identify what temperatures would be considered outliers. Q1-IQR = 67° (IQR = 6°) which shows that our mean temperature during the month of December falls inside the acceptable range of values for June. Histograms were generated to help illustrate the temperatures for each respective month. 

![june_temps_histogram](https://github.com/brand0j/Surfs_Up/blob/main/Resources/june_temps_histogram.png)
![december_temps_histogram](https://github.com/brand0j/Surfs_Up/blob/main/Resources/december_temps_histogram.png)

An additional query that might provide additional insight would be to look at the descriptive statistics for each month concerning precipitation (assuming people will be less likely to go surfing during poor weather conditions). To do this, we will make similar queries as we did to the temperature measurements, but just replacing Measurement.tobs with Measurement.prcp. The code we use is as follows:

```
form_one = r'\d{4}-06-\d{2}'
form_two = r'\d{4}-12-\d{2}'

june_precip = session.query(Measurement.date,Measurement.prcp).filter(Measurement.date.op('regexp')(form_one)).all()
dec_precip = session.query(Measurement.date,Measurement.prcp).filter(Measurement.date.op('regexp')(form_two)).all()

june_precip_df = pd.DataFrame(june_precip, columns = ['Date','June Precipitation'])
dec_precip_df = pd.DataFrame(dec_precip, columns = ['Date','December Precipitation'])
```

![june_precip](https://github.com/brand0j/Surfs_Up/blob/main/Resources/june_precip.PNG)
![december_precip](https://github.com/brand0j/Surfs_Up/blob/main/Resources/december_precip.PNG)

Looking at the descriptive statistics for each of our precipitation DataFrames it seems that there isn't a significant difference betweeen them. It might rain more on average during December, but it's within an acceptable range compared to June. Another interesting thing to note is when you use ```.groupby(['Date'], ascending=True)``` on the December DataFrame you'll notice when you compare ```.head(30)``` & ```.tail(30)``` that most of the lower temperatures occured a few years prior and that overall the average temperature year-to-year is gradually increasing. With all of the present information, I think it would be wise to advise W. Avy that it would be possible to remain open year round with the exception of a few colder/rainier days where it might be beneficial to close shop.
