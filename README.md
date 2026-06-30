# 🏠 Airbnb NYC Analytics Dashboard — Power BI

> An end-to-end data visualization project built on Airbnb New York City booking data, covering **1,00,000 bookings** across 20 neighbourhoods with insights on revenue, occupancy, hosts, and property performance.

---

## 🖼️ Dashboard Preview

![image alt](https://github.com/AryanInsights/AirBnB-Sales-Dashboard/blob/2378f5a407322188287a560ad680d58bd2f8ac75/Snapshot%20of%20Dashboard%201.png)

---

## 📌 Project Overview

This Power BI dashboard delivers a full analytical view of Airbnb's NYC listing and booking activity. It tracks revenue trends, occupancy rates, host performance, neighbourhood comparisons, and booking source breakdowns — all in an interactive, filterable interface.

| Metric | Value |
|---|---|
| 🏘️ Total Bookings | 1,00,000 |
| 💰 Total Revenue | $1,75,71,4781 (~$1.76 Cr) |
| 💵 Avg Nightly Rate | $235.53 |
| ⭐ Avg Review Score | 3.90 / 5.0 |
| 🏠 Total Properties | 80 |
| 👤 Total Active Hosts | 50 |

---

### 📋 Tables

#### `fact_bookings` (Central Fact Table)
| Column | Type | Description |
|---|---|---|
| booking_id | Text | Unique booking identifier |
| listing_id | Text | Property listing reference |
| host_id | Text | Host reference |
| date_key | Integer | FK to dim_date |
| location_id | Text | FK to dim_location |
| price | Integer | Nightly price in $ |
| minimum_nights | Integer | Minimum stay required |
| number_of_reviews | Integer | Review count |
| Review Score | Decimal | Guest rating (0–5) |
| availability_365 | Decimal | Days available in a year |
| calculated_revenue | Integer | Total booking revenue |
| cancellation_policy | Text | Flexible / Moderate / Strict |
| booking_source | Text | Channel (Airbnb App, Web, Direct, Partner, Corporate) |

#### `dim_property`
| Column | Type | Description |
|---|---|---|
| listing_id | Text | Unique listing ID |
| listing_name | Text | Property name |
| room_type | Text | Entire home / Private room / Hotel / Shared |
| property_type | Text | Apartment, Loft, Villa, Hostel, etc. |
| accommodates | Integer | Max guest capacity |
| bedrooms | Decimal | Bedroom count |
| bathrooms | Decimal | Bathroom count |
| amenities_count | Integer | Number of amenities |
| host_id | Text | FK to dim_host |
| location_id | Text | FK to dim_location |
| base_price | Integer | Base listing price |

#### `dim_host`
| Column | Type | Description |
|---|---|---|
| host_id | Text | Unique host ID |
| host_name | Text | Host name |
| host_since | Text | Date joined as host |
| host_response_rate | Decimal | Response rate (%) |
| superhost_flag | Text | Superhost / Regular |
| host_listings_count | Integer | Number of active listings |

#### `dim_location`
| Column | Type | Description |
|---|---|---|
| location_id | Text | Unique location ID |
| neighbourhood | Text | NYC neighbourhood |
| city | Text | City (New York) |
| state | Text | State |
| country | Text | Country |
| latitude | Decimal | Geo-coordinate |
| longitude | Decimal | Geo-coordinate |
| zipcode | Integer | ZIP code |

#### `dim_date`
| Column | Description |
|---|---|
| date_key | Surrogate date key |
| full_date | Full date value |
| year / quarter / month_num / month_name | Time hierarchy |
| week_num / day_of_week / day_name | Week-level breakdown |
| is_weekend / is_holiday | Boolean flags |

---

## 🏙️ NYC Neighbourhood Performance

| Neighbourhood | Bookings | Revenue |
|---|---|---|
| 🥇 Bronx | 12,573 | $26,100,428 |
| 🥈 Chelsea | 7,507 | $18,984,175 |
| 🥉 Crown Heights | 7,626 | $14,081,946 |
| Bushwick | 6,075 | $12,707,329 |
| Lower East Side | 6,151 | $11,687,422 |
| East Village | 7,441 | $11,402,148 |
| Williamsburg | 5,060 | $10,568,985 |
| Harlem | 7,519 | $10,136,206 |
| _(+ 12 more neighbourhoods)_ | | |

---

## 🏠 Room Type Breakdown

| Room Type | Bookings | Revenue |
|---|---|---|
| 🏡 Entire Home / Apt | 56,159 | $1,36,44,4881 |
| 🚪 Private Room | 31,231 | $2,36,58,170 |
| 🏨 Hotel Room | 6,391 | $1,25,84,618 |
| 🛋️ Shared Room | 6,219 | $30,27,112 |

---

## 📦 Booking Source Split

| Source | Bookings | Revenue |
|---|---|---|
| Partner | 20,086 | $35,720,793 |
| Corporate | 20,037 | $35,333,651 |
| Web | 20,063 | $35,085,808 |
| Direct | 19,931 | $34,740,719 |
| Airbnb App | 19,883 | $34,833,810 |

> Revenue is almost evenly split across all 5 booking channels.

---

## 🏆 Superhost vs Regular Host

| Host Type | Hosts | Revenue |
|---|---|---|
| ⭐ Superhost | 34 | $1,21,75,3215 |
| Regular | 16 | $5,39,61,566 |

Superhosts account for **69%** of total revenue despite being only 68% of hosts.

---

## 🧮 DAX Measures

All measures are stored in the `Measure Table`:

```dax
-- Total booking revenue
Total Revenue = SUM(fact_bookings[calculated_revenue])

-- Total number of bookings
Total Bookings = COUNTROWS(fact_bookings)

-- Average price per night
Avg Nightly Rate = AVERAGEX(fact_bookings, fact_bookings[price])

-- Revenue per available capacity
Revenue per Available Row =
    DIVIDE(
        [Total Revenue],
        SUMX(dim_property, dim_property[accommodates] * 365)
    )

-- Average revenue per booking
Avg Revenue per Booking = DIVIDE([Total Revenue], [Total Bookings], 0)

-- Occupancy rate based on availability
Occupancy Rate =
    DIVIDE(365 - AVERAGE(fact_bookings[availability_365]), 365)

-- Strict cancellation bookings ratio
Cancellation Rate =
    DIVIDE(
        CALCULATE([Total Bookings], fact_bookings[cancellation_policy] = "strict"),
        [Total Bookings]
    )

-- Average minimum nights per booking
Avg Nights Per Booking = AVERAGE(fact_bookings[minimum_nights])

-- Previous month revenue (time intelligence)
Revenue PM =
    CALCULATE([Total Revenue], DATEADD(dim_date[full_date], -1, MONTH))

-- Same period last year revenue
Revenue PY =
    CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(dim_date[full_date]))

-- Year-over-year growth %
YoY Revenue Growth% =
    DIVIDE([Total Revenue] - [Revenue PY], [Revenue PY])

-- Average guest review score
Avg Review Score = AVERAGE(fact_bookings[Review Score])

-- % of bookings with rating >= 4.5
High Rating % =
    DIVIDE(
        CALCULATE([Total Bookings], fact_bookings[Review Score] >= 4.5),
        [Total Bookings]
    )

-- Revenue from Superhost listings only
Superhost Revenue =
    CALCULATE([Total Revenue], dim_host[superhost_flag] = "Superhost")

-- Total active hosts
Total Active Hosts = COUNT(dim_host[host_id])
```

---

## 🛠️ Tools & Technologies

| Tool | Usage |
|---|---|
| **Power BI Desktop** | Dashboard development & visualization |
| **DAX** | Custom measures & time intelligence |
| **Power Query (M)** | Data transformation & cleaning |
| **Star Schema** | Data modeling approach |

---

## 📊 Key Insights

- 🏆 **Bronx** leads in both bookings (12.5K) and revenue ($26M) among all neighbourhoods
- 🏡 **Entire Home/Apt** is the most booked room type at 56% of total bookings
- ⭐ **Superhosts** generate nearly 70% of total revenue
- 📊 All **5 booking channels** (App, Web, Direct, Corporate, Partner) are nearly equal in volume
- 💰 **Loft** properties command the highest average nightly rate at $327/night
- 🏨 **Hostels** are the most frequently booked property type (16,181 bookings)
- 📅 **YoY and MoM** time intelligence helps track seasonal revenue trends

---

👤 Author

Aryan Kumar


📧 Email: aryankumar.ak929@gmail.com
💼 LinkedIn: https://www.linkedin.com/in/aryankumar4
🐙 GitHub: https://github.com/AryanInsights


## 📄 License

This project is for educational and portfolio purposes only.

Data used is synthetic/sample data inspired by Airbnb NYC open datasets.

---

⭐ **If you found this project helpful, give it a star!**
