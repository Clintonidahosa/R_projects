🏙️ Airbnb Private Room Analysis – New York
As a consultant working for a real estate start-up, your task is to investigate the short-term rental market in New York City using Airbnb listing data. The objective is to analyze private rooms and provide key insights to the real estate company.

📁 Dataset Overview
Three files located in the data/ folder were used:

airbnb_price.csv – Contains listing prices

airbnb_room_type.xlsx – Contains room type information

airbnb_last_review.tsv – Contains dates of last reviews

🧩 Business Questions
🗓️ What are the dates of the earliest and most recent reviews?

🛏️ How many of the listings are private rooms?

💰 What is the average listing price for all rooms (rounded to the nearest penny)?

📊 Combine these insights into a summary tibble called review_dates with four columns:

first_reviewed

last_reviewed

nb_private_rooms

avg_price







# Load Required Packages
suppressMessages(library(dplyr))
options(readr.show_types = FALSE)
library(readr)
library(readxl)
library(stringr)
library(data.table)

# Read Data
price <- read_csv("data/airbnb_price.csv")
room <- read_excel("data/airbnb_room_type.xlsx")
review <- read_tsv("data/airbnb_last_review.tsv")

# Merge Datasets
price_room <- price %>%
  full_join(room, by = "listing_id")

price_room_review <- price_room %>%
  full_join(review, by = "listing_id")

# 1. Earliest and Latest Review Dates
earliest_review <- price_room_review %>%
  arrange(as.Date(last_review, format = "%B %d %Y")) %>%
  slice(1)

latest_review <- price_room_review %>%
  arrange(desc(as.Date(last_review, format = "%B %d %Y"))) %>%
  slice(1)

# 2. Number of Private Rooms
private_rooms <- price_room_review %>%
  filter(grepl("vate", room_type, ignore.case = TRUE))

# 3. Average Price
avg_price <- price_room_review %>%
  mutate(num_price = as.numeric(gsub("dollars", "", price, ignore.case = TRUE))) %>%
  summarize(avg_price = round(mean(num_price, na.rm = TRUE), 2))

# 4. Combine into Tibble
review_dates <- tibble(
  first_reviewed = as.Date(earliest_review$last_review, format = "%B %d %Y"),
  last_reviewed = as.Date(latest_review$last_review, format = "%B %d %Y"),
  nb_private_rooms = nrow(private_rooms),
  avg_price = avg_price$avg_price
)

# Output
glimpse(review_dates)
print(review_dates)
