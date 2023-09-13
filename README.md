**Napoleons-March-Minard-Visualization**

The goal of this excercise is to reproduce, to some reasonable approximation, Minard’s famous graphic of Napoleon’s March on Moscow. Along the way, we’ll learn some techiques for developing plots using ggplot2.

The first step is to understand the available data.

The data are contained in three data.frames in the HistData package.

1. Now, we can recreate the plot of Napoleon’s march in 1812. Parameters included are Latitude, Longitude, troop strength (size) and temperature.

```
#Troop data
    data(Minard.troops, package="HistData")
    str(Minard.troops)
#Cities Data
    data(Minard.cities, package="HistData")
    str(Minard.cities)
#Temperature data
    data(Minard.temp, package="HistData")
    str(Minard.temp)
```

2. Below plot is for plotting the latitude and longitude the troops travelled
Width is set as the number of survivors to show the strength of the army
Knitr function is used to scale the graph.

```
 breaks <- c(1, 2, 3) * 10^5 #New vector setis defined
    ggplot(Minard.troops, aes(long, lat)) +
            geom_path(aes(size = survivors, colour = direction, group = group),
                      lineend="round") +
        scale_size("Survivors", range = c(1,10), #c(0.5, 15),
                   breaks=breaks, labels=scales::comma(breaks)) +
        scale_color_manual("Direction", 
                           values = c("cornsilk3", "black"), 
                           labels=c("Advance", "Retreat")
    #ggplot trick to assign it to a variable to use later
    plot_troops <- last_plot()
```
Output for above:

![1](https://github.com/shakir-flash/Napoleons-March-Minard-Visualization/assets/59859522/a6eccda6-7db1-45f8-8974-0aae5d2ec996)

Now, we plot the cities data over the graph:
```
#Cities are now appended on top of the above plot
    plot_troops + geom_text(data = Minard.cities, aes(label = city), size = 3)
```
Output for above:

![2](https://github.com/shakir-flash/Napoleons-March-Minard-Visualization/assets/59859522/441814db-e9bd-45ec-ba54-d2717607f118)


3. Package ggrepel is used to automatically move the labels away from points and to ensure none of the labels overlap

```
if (!require(ggrepel)) {install.packages("ggrepel"); require(ggrepel)}
    library(ggrepel)
    plot_troops +   
        geom_point(data = Minard.cities) +
        geom_text_repel(data = Minard.cities, aes(label = city))

    #ggplot trick to assign it to a variable to use later
    plot_troops_cities <- last_plot()
```
Output for above:

![3-1](https://github.com/shakir-flash/Napoleons-March-Minard-Visualization/assets/59859522/fd2b2e3d-0cdf-43c3-b011-cf23ee1bfec4)


4. Plotting the temperature with custom size
```
ggplot(Minard.temp, aes(long, temp)) +
        geom_path(color="grey", size=1.5) +
        geom_point(size=2)
```
Output for above:

![4](https://github.com/shakir-flash/Napoleons-March-Minard-Visualization/assets/59859522/b50fe4a7-9e87-4fa3-b952-e5fbeef1b018)


5. Below code is to standardize the temperature format by combining temperature and date:
```
 Minard.temp <- Minard.temp %>%
        mutate(label = paste0(temp, "° ", date))
    head(Minard.temp$label)
```

6. These plots are pushing the right most two points outside the graph. Using geom_text_repel to bring back the dates
```
ggplot(Minard.temp, aes(long, temp)) +
        geom_path(color="grey", size=1.5) +
        geom_point(size=1) +
      geom_text_repel(aes(label=label), size=2.5)

    #ggplot trick to assign it to a variable to use later
    plot_temp <- last_plot()
```
Output for above:

![4-1](https://github.com/shakir-flash/Napoleons-March-Minard-Visualization/assets/59859522/e12a3c89-77d5-4da7-be02-d712760b427a)


7. Now, we have to assemble the two graphs one below the other
```
    grid.arrange(plot_troops_cities, plot_temp)
```

Output for above:

![111](https://github.com/shakir-flash/Napoleons-March-Minard-Visualization/assets/59859522/686964de-33b5-4759-b238-804d3af2d4e3)


8. In troops and cities plot, we have to set X axis limits to a range that will coincide with those for the plot of temperature
Remove X and Y axis labels scales
Remove legends for survivors and direction
```
plot_troops_cities +
      coord_cartesian(xlim = c(24, 38)) +
      labs(x = "Longitude", y = "Latitude",title="Minard's version of Napoleon's March") +
      guides(color = FALSE, size = FALSE)

    #ggplot trick to assign it to a variable to use later
    plot_troops_cities_fixed <- last_plot
```

9. Setting theme for temperature graph
```
plot_temp + 
      coord_cartesian(xlim = c(24, 38)) +
      labs(x="Napoleon's March Duration", y="Temperature",caption = "Michael Friendly (2018), Minard meets ggplot2") + 
      theme_bw() +
      theme(panel.grid.major.x = element_blank(),
            panel.grid.minor.x = element_blank(),
            panel.grid.minor.y = element_blank(),
            axis.text.x = element_blank(), axis.ticks = element_blank(),
            panel.border = element_blank())

    #ggplot trick to assign it to a variable to use later
    plot_temp_fixed <- last_plot()

    #Function to combine both the plots into one single plot and add a border
    grid.arrange(plot_troops_cities_fixed, plot_temp_fixed, nrow=2, heights=c(3.5, 1.2))
    grid.rect(width = .99, height = .99, gp = gpar(lwd = 2, col = "gray", fill = N
```
Final output of Napoleon's advance and retreat in 1812:

![8-fin](https://github.com/shakir-flash/Napoleons-March-Minard-Visualization/assets/59859522/68974777-df2e-4a02-80e3-d83ae4cce5a6)

























