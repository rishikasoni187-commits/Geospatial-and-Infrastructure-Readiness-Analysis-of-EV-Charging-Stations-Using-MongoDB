# Geospatial-and-Infrastructure-Readiness-Analysis-of-EV-Charging-Stations-Using-MongoDB
An analytical study of EV charging infrastructure to identify geographic gaps, operator dominance, and system readiness for large-scale EV adoption.


 
**Term – 3 | Big Data Management & Analytics Group Project**



SUBMITTED BY: Group - 7
Armaan Lakhanpal -(065069)
Ashwin Soni - (065071)
Kavya Dangwal - (065082)
Rishika Soni - (065099)

SUBMITTED TO:
Mr. Aditya Dua



Table of Contents

1.	Executive Summary

2.	Problem Statement

3.	Dataset Overview

4.	Data Engineering & Normalization

5.	Geographic Distribution Analysis

6.	Operator Market Structure

7.	Spatial Clustering Analysis

8.	Connector Type Distribution

9.	Power Maturity Matrix

10.	 Strategic Insights

11.	 Conclusion

 
**Executive Summary**

India's shift to electric vehicles relies heavily on how much charging infrastructure there is, where it's located, and the type of power it uses. This study uses MongoDB Atlas to examine 1,324 electric vehicle charging stations obtained from the OpenChargeMap API.
The project evaluates:
•	Geographic distribution of stations
•	Operator market structure
•	Spatial clustering
•	Connector current type dominance (AC vs DC)
•	Infrastructure power maturity

The results show that most of the activity is happening in the southern part of India. There is some merging of companies, but not a lot. The number of fast-charging stations is growing quickly. There's also a move towards building more powerful charging systems, but very fast charging is still not very common.

This study shows how document-based NoSQL systems can turn semi-structured infrastructure data into useful strategic infrastructure insights.

**Problem Statement**

India's electric vehicle adoption is speeding up, but the way charging and supporting infrastructure is being set up is not the same everywhere and is still changing over time.

The main questions that were looked at in this study are:
•	How are electric vehicle charging stations spread out in different states of India?
•	How focused are the operators in the market?
•	Are charging stations grouped together in city areas?
•	Is India's infrastructure mainly using AC (slow) or DC (fast) connectors?
•	What is the current level of development in India's charging infrastructure?

The goal is to check if India's electric vehicle charging network is in the early stage, moving towards more growth, or already well-established.

**Dataset Overview**

The dataset was brought into MongoDB Atlas using the OpenChargeMap API.

•	Database: ev_mapping
•	Collection: EV_mapping
•	Documents: 1,324
•	Format: JSON (Document-Oriented)

Each document contains nested structures:
•	AddressInfo (location metadata)
•	OperatorInfo (service provider)
•	Connections (array of connectors)
•	PowerKW (charging capacity)
•	Latitude & Longitude (geospatial coordinates)

MongoDB is a good choice for analytical work because it can manage arrays and objects that are inside other objects, without needing to use joins like in traditional databases.

**Data Engineering & Normalization**

Initial data exploration showed 59 different state entries because of:
•	Case mismatches
•	Trailing spaces
•	Abbreviations (MH, RJ, KA)
•	Typographical errors
•	City names stored as states
  

To make sure the aggregation is accurate, normalization was done using:
•	$trim
•	$toUpper
•	Manual abbreviation mapping

 Distinct state entries were simplified into 32 clear categories.

This highlights a key managerial insight:

Infrastructure analytics depends on the quality of the data it uses. If the data is not accurate or complete, the analytics results won't be trustworthy.

**Geographic Distribution Analysis**

*  Problem Statement 1
To look at how electric vehicle charging stations are spread out across different states in India and find out where they are mostly concentrated.

 
Key Findings
•	Kerala - 512
•	Karnataka - 353
•	Tamil Nadu - 182

**Inference**
Southern India dominates charging infrastructure deployment. The top three states have a much larger number of stations compared to other states, showing an uneven spread of infrastructure and suggesting that these areas get more attention from policies or private companies.


**Operator Market Structure**

*  Problem Statement 2
To check how much control operators have and understand how the competition works in India's electric vehicle charging network.


Key Findings
•	ChargeMod - 268
•	Zeon Charging - 160
•	Tata Power - 156

**Inference**

Although there are 31 active operators, the infrastructure is somewhat concentrated among the leading providers. This shows that the market is starting to consolidate, and companies that moved in first are gaining an edge.

**Spatial Clustering Analysis**

*  Problem Statement 3
To figure out if electric vehicle charging stations are mostly located near cities and towns.



**Inference**
A lot of people are gathering together in Bangalore, Kochi, and Chennai. Sparse deployment of resources in central and northeastern India shows a preference for urban areas and indicates that the country is not equally prepared across all regions.
 
**Connector Type Distribution**

*  Problem Statement 4
To look at how electric vehicle charging connectors are spread out based on whether they are AC or DC and to check the different parts that make up India's charging network.

**Aggregation Used**
[
  { $unwind: "$Connections" },
  {
    $group: {
      _id: { currentType: "$Connections.CurrentType.Title" },
      totalConnectors: { $sum: 1 },
      avgPower: { $avg: "$Connections.PowerKW" }
    }
  },
  { $sort: { totalConnectors: -1 } }
]
 


**Inference 4**
DC connectors are used much more than AC connectors, showing a big change in how charging infrastructure is being built.

This suggests:
•	Expansion of rapid charging networks
•	Improved long-distance EV usability
•	Infrastructure aligned with commercial mobility and highways
•	AC connectors are still around but are mainly used for urban and residential purposes.


**Power Maturity Matrix**

*  Problem Statement 5
To check how mature the infrastructure is, look at the connectors used in different power categories and types of current.

**Aggregation Used**
[
  { $unwind: "$Connections" },
  {
    $addFields: {
      powerCategory: {
        $switch: {
          branches: [
            { case: { $lt: ["$Connections.PowerKW", 20] }, then: "Low (<20kW)" },
            { case: { $and: [
                { $gte: ["$Connections.PowerKW", 20] },
                { $lt: ["$Connections.PowerKW", 50] }
              ]}, then: "Medium (20-50kW)" },
            { case: { $and: [
                { $gte: ["$Connections.PowerKW", 50] },
                { $lt: ["$Connections.PowerKW", 150] }
              ]}, then: "Fast (50-150kW)" }
          ],
          default: "Ultra Fast (>150kW)"
        }
      }
    }
  },
  {
    $group: {
      _id: {
        currentType: "$Connections.CurrentType.Title",
        powerCategory: "$powerCategory"
      },
      total: { $sum: 1 }
    }
  }
]
 


**Inference 5**
DC connectors are mostly used in the Fast charging range, which is between 50 and 150 kilowatts.
AC connectors are most commonly used in the Low and Medium power categories.
Ultra-fast (>150 kW) infrastructure remains limited.

This indicates:
•	Infrastructure maturity is improving.
•	India is transitioning toward fast-charging dominance.
•	Ultra-fast charging remains in early adoption stage.
The ecosystem shows a change in structure instead of being completely mature.

**Strategic Insights**

•	Southern India leads infrastructure readiness.
•	Market shows moderate operator consolidation.
•	Urban clustering dominates deployment.
•	DC fast charging is structurally dominant.
•	Ultra-fast infrastructure is still emerging.
•	Data normalization is critical in infrastructure analytics.

**Conclusion**

This study shows how well MongoDB can work with semi-structured data from infrastructure systems.

The EV ecosystem in India shows:
•	Regional concentration
•	Operator consolidation
•	Rapid DC charging expansion
•	Transitional power maturity
•	Urban-centric deployment

India's electric vehicle charging network is moving more towards fast charging, but it still hasn't fully developed the structure needed for ultra-fast charging.


MongoDB Atlas proved effective for:


*  	Nested document handling
*  	Aggregation-based analytics
*  	Connector-level analysis
*  	Geospatial visualization
* 	Infrastructure maturity assessment
