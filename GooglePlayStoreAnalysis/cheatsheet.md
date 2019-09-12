
# R Workshop | Google Play Store Data Analysis

The below markdown file consists of commands and code snippets that will help you complete the lab - Google Play Store Data Analysis

## Code Snippets

### Install Packages

```
#Install and Import the packages and libraries which are necessary for our R application.

install.packages("ggplot")

install.packages("plotly")

library(ggplot2)

library(plotly)
```

### Read the Data set

```
data <- read.csv("GooglePlaystoreDataset.csv",stringsAsFactors=FALSE)

#To check for NULL Values in dataset

data <- data[complete.cases(data), ]

is.na(data)
```

## Top Rating Apps
```
TopCategoryApp <- aggregate(Rating~Category, data, max)

total <- merge(data,TopCategoryApp,by=c("Category","Rating"))

df = total[-1,]

Reorder<-df[order(df$Rating,decreasing = TRUE),]

Top <- head(Reorder,10)

agg_data<-select(Top,c(1,2,3))
```

### Plot Bar graph in ggplot2

```
Top_Rating<-ggplot(agg_data, aes(App, Rating,fill=App))

Top_Rating +geom_bar(stat = "identity") +

xlab("Apps") + ylab("") +

ggtitle("Top 10 Rating Apps") +

theme(axis.text.x = element_text(angle=15, hjust=1),legend.position="none")
```

## Finding installations count in each category

```
data$Installs<-gsub('^[+]*|[+ ]*$', '', data$Installs)

data$Installs <- gsub(",", "", data$Installs)

data$Installs<-as.numeric(as.factor(data$Installs))

sum_install <- aggregate(Installs~Category, data, sum)

df1 = sum_install[-1,]
```

### Plot Pie Chart

```
Pie_categoryinstalls <- plot_ly(sum_install, labels = ~Category, values = ~Installs, type = 'pie') %>%

  layout(title = 'Total Installations in each Category',
  
         xaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
         
         yaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE))
         
Pie_categoryinstalls
```

## Top Apps in Paid Category
```
Paid_Apps<-subset(df,Type=="Paid")
```

### Plot plotly graph

```
Free_Paid_Apps<-ggplot(data = Paid_Apps, aes(x = Category, y = Rating)) +

  geom_bar(aes(fill = App), stat = "identity") +
  
  theme(axis.text.x = element_text(angle=25, hjust=1),legend.position="none") +
  
  xlab("Apps") + ylab("Count") + ggtitle("Top Apps in Paid category")
  
ggplotly(Free_Paid_Apps)
```
