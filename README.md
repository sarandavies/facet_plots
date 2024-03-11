# It's a wrap - how to get the best out of creating multi plot panels in R
## Pretty facets in R

# Overview:
#1) The different types of facets (facet_wrap vs facet_grid)
#2) Facets with check marks
#3) Facet spacing
#4) Editing titles
#5) Tip for arranging faceted plots with ggarrange


# Packages:
library(ggplot2) #the classic
library(tidyverse) #in case data manipulation needed
library(dplyr) #in case data manipulation needed
library(jtools) #clear plotting visuals
library(lemon) #key package for facet_rep_wrap (to give you y axis tick marks)

# We will use the iris dataset:
data(iris)
head(iris)
## The iris dataset gives the measurements in cm of the variables sepal length, sepal width, petal length and petal width, respectively, for 50 flowers from each of 3 species of iris. The species are Iris setosa, versicolor, and virginica.

# 1) Easy facet wrap
simple <- ggplot(iris, aes(x=Sepal.Length, y =Sepal.Width, color = Species))+
  geom_point()+
  labs(x= 'Sepal Length (cm)', y = 'Sepal Width (cm)', title = 'Simple facet_wrap')+
  scale_color_brewer(palette = 'Accent')+ #setting a color theme
  theme_apa()+ #from the jtools package
  theme(legend.position = 'none')+ #removing the legend since individual facets have labels
  facet_wrap(.~Species) #faceting by flower species
simple


grid <- ggplot(iris, aes(x=Sepal.Length, y =Sepal.Width, color = Species))+
  geom_point()+
  labs(x= 'Sepal Length (cm)', y = 'Sepal Width (cm)', title = 'Simple facet_grid')+
  scale_color_brewer(palette = 'Accent')+
  theme_apa()+
  theme(legend.position = 'none')+
  facet_grid(.~Species)
grid

ggarrange(simple, grid, nrow = 2)

## At this stage they're identical. However, if we want to have free y axes, then we can only use facet_wrap with the argument 'scales = 'free_y'

simple_free <- ggplot(iris, aes(x=Sepal.Length, y =Sepal.Width, color = Species))+
  geom_point()+
  labs(x= 'Sepal Length (cm)', y = 'Sepal Width (cm)', title = 'facet_wrap with free_y')+
  scale_color_brewer(palette = 'Accent')+
  theme_apa()+
  theme(legend.position = 'none')+
  facet_wrap(.~Species, scales='free_y')

ggarrange(simple, simple_free, nrow = 2)

# 2) Adding check marks
## Since our plots don't have grid lines, comparing the y axes across multiple panels can be tricky this can be helped by using facet_rep_wrap from the 'lemon' package

tick_marks <- ggplot(iris, aes(x=Sepal.Length, y =Sepal.Width, color=Species))+
  geom_point()+
  labs(x= 'Sepal Length (cm)', y = 'Sepal Width (cm)', title = 'Adding y axis tick marks with facet_rep_wrap')+
  scale_color_brewer(palette = 'Accent')+
  theme_apa()+
  theme(legend.position = 'none')+
  facet_rep_wrap(.~Species)

ggarrange(simple, tick_marks, nrow = 2)

## The plot spacing can be irritating here, especially if you have very big or very small numbers (with lots of zeros/decimal places) a way round this is to manipulate panel spacing (where you can add or remove space by using positive or negative values):

# 3) Facet spacing
## We can edit the panel spacing using panel.spacing. Luckily this works with both positive and negative numbers

tick_marks_2 <- ggplot(iris, aes(x=Sepal.Length, y =Sepal.Width, color=Species))+
  geom_point()+
  labs(x= 'Sepal Length (cm)', y = 'Sepal Width (cm)', title = 'Changing the spacing between panels')+
  scale_color_brewer(palette = 'Accent')+
  theme_apa()+
  theme(panel.spacing = unit(-0.5, 'lines'), legend.position = 'none')+
  facet_rep_wrap(.~Species)
tick_marks_2

ggarrange(tick_marks, tick_marks_2, nrow =2)


# 4) Editing facet labels
## For this example, I will change the facet label to italics
tick_marks_3 <- ggplot(iris, aes(x=Sepal.Length, y =Sepal.Width, color=Species))+
  geom_point()+
  labs(x= 'Sepal Length (cm)', y = 'Sepal Width (cm)', title = 'Making the facet titles italicised')+
  scale_color_brewer(palette = 'Accent')+
  theme_apa()+
  theme(panel.spacing = unit(-0.5, 'lines'), legend.position = 'none')+
  facet_rep_wrap(.~Species)+
  theme(strip.text = element_text(face = "italic"))
tick_marks_3


# 5) Aligning multiple facet panels with ggarrange
## Sometimes it's easier to create a figure with multiple panels compiled together. However, this can be tricky to achieve when y axes labels have differing numbers of decimal places. Luckily we can manipulate ggarrange aligning to help

## un-aligned and difficult to compare:
ggarrange(simple, tick_marks, nrow = 2) 

## aligned using simple trick of 'align = 'v''
ggarrange(simple, tick_marks, nrow =2, align ='v')


