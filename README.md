[EnterpriseDNA Power BI Challenge 10 - Supplier Insight](https://forum.enterprisedna.co/t/power-bi-challenge-10-supplier-insight/12521)

Interact it with it here: [Novypro Interactive Link](https://www.novypro.com/project/supplier-insight-enterprise-dna-challenge-10)

![Dashboard Snip](Artefacts/Dashboard%20Snip.png)

## The Brief

You are working with a manufacturer who receive and order a number of raw materials which are then used in production or for general maintenance. Currently there is no procurement system in place and no way for the companies to validate which suppliers are providing us with quality goods and which are not. There is also no consistency between different plants and the vendors we are purchasing from. The programme management team have identified the need to centralise and understand supplier quality as a priority. There has been a major effort in recent weeks to consolidate the data. The team have now managed to gather data from across the plants with information around the material, defect and vendor. They have also managed to get the number of defected materials and also provided a value for the minutes of downtime caused by the defected material. The management team are now looking for some help to visualise and extrapolate the findings from this data.

Enterprise Manufacturers Ltd are slowly adopting Power Bi within their organisation as such one of the analysts has made an effort at starting to model the data. Given the importance of the project and urgency management have decided to enlist the experts to get this over the line.

Some key questions the business want answering are;

- Which vendors/plants are causing the greatest defect quantity?

- Which vendors/plants are causing the greatest downtime?

- Is there a particular combination of material and vendor that perform poorly?

- Is there a particular combination of Vendor and plant that performs poorly?

- How does the same vendor and material perform across different plants?

- The business are hoping that you can help answer these questions and maybe even provide some insights that they may have overlooked.


At the moment this data is not available as the business are struggling to get the data out of the system.

It has been agreed that if the management team see value in this first iteration of Power BI development they would be willing to invest in the technical expertise required to extract the purchasing quantity.

The management team are happy to accept in this first instance they wont be able to see percentage of defects compare to purchased amount.




## ETL Process

The provided .xlsx contained most dimensions in separate tables from the Supplier Quality fact table. To ensure a proper one-to-many relationship, duplicates in dimensions were removed. A vendor dimension and a comprehensive Date table were created, with the Date table marked accordingly.

## Modelling

![Model Snip](Artefacts/Model%20Snip.png)

Each of the 7 dimensions was linked to the fact table using a one-to-many regular relationship. The import storage option was used. Foreign keys on the fact table were hidden, adhering to best practices.

### Measures

Defect Quantity = CALCULATE(SUM('Supplier Quality'[Total Defect Qty]))  
This calculates the total defective materials

Percentage of Total Defect Qty = 
VAR _TotalQty = [Total Defect Quantity]
VAR _SelectedQty = [Defect Quantity]
RETURN DIVIDE(_SelectedQty,_TotalQty,0)

This calculates the percentage of defective materials for the selected or highlighted dimension out of the total defective materials. This is used as a detail measure in a card reference label

Plant = SELECTEDVALUE(Plant[Plant]). This highlights the name of the selected plant. This is used as part of a tooltip visual

Top N Defect Quantity = 
VAR SelectedN = 'TopN'[TopN Value]
RETURN
CALCULATE(
    [Defect Quantity],
    KEEPFILTERS(
            TOPN(
            SelectedN, 
        ALLSELECTED(Plant[Plant]), 
            [Defect Quantity], 
            DESC
            )
    )
)

This is used as a measure for a bar chart that responds  to a TopN slicer allowing users/report consumers greater flexibility

Total Defect Quantity = CALCULATE(SUM('Supplier Quality'[Total Defect Qty]), ALL('Supplier Quality'))
This shows the overall number of defective materials irrespective of the filters on the report

Total Downtime = 
DIVIDE(
        SUM('Supplier Quality'[Total Downtime Minutes]),
        60, 
        0
)
This calculates the total downtime and converts it to hours. It was formatted with the following format string "0,0" & " Hours"


## Visuals

**Map**: Showing plants location (which have been marked as a City Data category). The size of the bubbles depends on the defect quantity volume and the colour was conditionally formatted based on the total time downtime.
! [Map Visual](Artefacts/Map%20visual%20Snip.png)

	Associated Tooltip Page: A tool tip page that shows the name of the plant, the total downtime, the total volume of defective materials, and a line chart that shows the trend of both downtime and defective materials over the selected time range

! [Map Tooltip](Artefacts/Map%20tooltip%20Snip.png)

Rationale: To show at a glance the regions/cities with the most downtime and/or defective products. Selecting a plant in this visual will further filter (not highlight) other visuals in this report) to see the top vendors supplying defective products and the type of defective materials supplied


**Stacked Bar Chart**: Showing vendors ranked according to the volume of defective materials. This is broken down into the three defect type categories: Impact, No Impact, and Rejected. Data values were turned on as well as the total values label

! [Vendor Visual](Artefacts/Vendors%20Visual%20Snip.png)
	Associated Tooltip Page: A tool tip page that shows in a bar chart, the volume of defective materials sorted descendingly for each defect type category

! [Vendor Tooltip](Artefacts/Vendors%20tooltip%20Snip.png)

Rationale: Some materials though defective have no impact ostensibly on the downtime so it's proper to know the materials with the most impact. This also filters the next visual which shows the top plants affected by plants. So for each vendor, we can see the top plants that are on the receiving end of their inefficiencies.

**Column Chart**: Showing Top plants affected by plants. This is tied to a TopN slicer (created with parameters) that selects between 5 and 30 in steps of 30. This visual filters other visuals and is in turn filtered by other visuals.

! [Plant Visual](Artefacts/Plants%20Visual%20Snip.png)

        Associated Tooltip Page: A tool tip page that shows the name of the plant, the total downtime, the total volume of defective materials, and a line chart that shows the trend of both downtime and defective materials over the selected time range

! [Map Tooltip](Artefacts/Plant%20tooltip%20%20Snip.png)

Rationale: To show the relationship between materials, vendors and plants

**Card**: Showing the total downtime, overall Defective Quantity, the defective quantity for the current selection and the percentage of the latter of the former. 

Rationale: To show import metrics at a glance

**Defect Type Slicer**: To possibly filter off records that have no impact 

! [Other Visuals](Artefacts/Other%20visuals%20Snip.png)

## Design

The background was created in Figma  and exported as SVG to ensure a proper gradient fill. Page canvas size is the default 16:9 (1280 x 720). 

## Miscellaneous

This challenge is a reflection of real-life situations where data is not readily available. The idea is then to build a Minimum Viable Product (MVP) as you collect requirements and new data is obtained.
