suppressMessages(library(dplyr)) # This line is required to check your answer correctly
options(readr.show_types = FALSE) # This line is required to check your answer correctly
library(readr)
library(readxl)
library(stringr)
library(data.table)

price <- read_csv("data/airbnb_price.csv")
head(price)
room <- read_excel("data/airbnb_room_type.xlsx")
excel_sheets("data/airbnb_room_type.xlsx")
head(room)
review <- read_tsv("data/airbnb_last_review.tsv")
head(review)

#joining price and room
price_room <- price %>%
	            full_join(room, by="listing_id")
glimpse(price_room)

#joining price_room and review
price_room_review <- price_room %>%
						full_join(review, by = "listing_id")
glimpse(price_room_review)

#dates of earliest and most recent review
earliest_review <- arrange(price_room_review, as.Date(last_review, format = "%B %d %Y"))
ER <- slice(earliest_review, 1)
ER$last_review
latest_review <- arrange(price_room_review, desc(as.Date(last_review, format = "%B %d %Y")))
LR <- slice(latest_review, 1)
LR$last_review

#no of private rooms
PR <- price_room_review %>%
		filter(grepl("vate", room_type, ignore.case = TRUE))
					
length(PR$room_type)

#avg listing price for all rooms
glimpse(price_room_review)
distinct(price_room_review, price)
Avg_room_price <- price_room_review %>%
		mutate(num_price = as.numeric(gsub("dollars", "", price, ignore.case = TRUE)))%>%
		summarize(Avg_num_price = round(mean(num_price), 2))
Avg_room_price$Avg_num_price


#merging all into a tibble called review dates
headings = c("first_reviewed", "last_reviewed", "nb_private_rooms", "avg_price")
elements = c(ER$last_review, LR$last_review, length(PR$room_type), Avg_room_price$Avg_num_price)

review_dates <- tibble(
	first_reviewed = as.Date(ER$last_review, format = "%B %d %Y"), 
	last_reviewed = as.Date(LR$last_review, format = "%B %d %Y"),
	nb_private_rooms = length(PR$room_type), 
	avg_price = Avg_room_price$Avg_num_price
	
)

glimpse(review_dates)
review_dates
