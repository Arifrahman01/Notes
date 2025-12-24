Modeler = Model 2 Test
CDS     = 2CZVBMMXX002_CV

--------------------------------------------------------------------------
- Total Supplier Active In 3 Month
	- Type Chat 
		- Numeric Point
	- Source Data
		- Total Supplier
			- Calculation
				- Operation = Count  
				- Aggeration = Supplier
			- Filter 
				- FLAG 3 Month = 1
				
- Total Purchase Document
	- Type Chart
		- Numeric Point
		- Source Data
			- Total Purchase Document
				- Calculation
					- Operation = Count
					- Aggeration =  Purchasing Document
						
- Total Purchase Order (IDR)
	- Type Chart
		- Numeric Point
	- Source Data
		- Value Purchase Order (Value Ordered)
			
- Total PO Received Item
	- Type Chart
		- Numeric Point
	- Source Data
		- Qty PO Received (POQuantityReceived)
			
- Total PO Outstanding Item
		- Type Chart
			- Numeric Point
		- Source Data
			- Qty PO Outstanding (POQuantityOutstanding)
			
- Average Duration from Purchased Order Release /Until Delivered (Days)
	- Type Chart 
		- Numeric Point
	- Source Data
		- PO Release VS PO Received
			- Calculation
				- Operation = AVERAGE excl. 0, Null
				- Measure = Average Days
				- Aggregation = Purchasing Document
			
- Fasted Delivered Item
	- Type Chart
		- Numeric Point
	- Source Data
		- Faster_Dur_Min
			- Calculation
				- Operation = MIN
				- Measure = Average Days
				- Aggregtion = Purchasing Document 
		- Filter
			- FASTSTPR = 1
			
-  Slowlest Delivered Item
	- Type Chart
		- Numeric Point
	- Source Data
		- Slowlest_Dur_Max
			- Calculation
				- Operation = MAX
				- Measure = Average Days
				- Agregation = Purchasing Document
		- Filter 
			- SLOWLESTPR = 1
--------------------------------------------------------------------------
- Pareto Total Trans. By Supplier
	- Type Chart
		- Combination Column and Line
	- Source Data
		- Column Axist
			- Line Item
				- Calculation
					- Operation = Max
					- Measure = LINEITEM
					- Aggregation
						- Supplier Name
						- ProcurmentCategory
		 - Line Axist
			 - Supplier
				 - Calculation
					 - RUNALL_PCT_ITEM / 100
					 
- Top Supplier Performance
	- Type Chart
		- Table
	 - Source Data
		 - Measure
			 - PerformanceGods
			 - AverageDays
		- Filter 
			- Value PO Received
			- Goods Quantity Received
			- Performance Gods
			- Average Days
			
- PO Outstanding qty VS Value By Supplier
	- Type Chart
		- Combination Column & Line
	- Source Data
		- Column Axis
			- Value PO Outstanding (POValueOutstanding)
		 - Line Axist
			 - QTY PO Outstanding (POQuantityOutstanding)
		 - Dimension
			 - Supplier Name
--------------------------------------------------------------------------
- Supplier VS PO Release
	- Type Chart
		- Line
	 - Source Data
		 - Line y Axis
			 - Supplier Count 
				 - Operation = Cont Dimension
				 - Aggregation = Supplier Name
		- Right y Axis
			- PO Release Quantity
				- Operation = SUM
				- Measure = Total Purchased Document
				- Aggregation = CalenderMonthPO
		- Color
			- Supplier Count
			- PO Release Quantity

- Top Purchase Order Value by Material
	- Type Chart
		- Bar
	- Source Data
		- Value Purchase Order (ValueOrdered)

- Top Purchase Order Quantity by Material
	- Type Chart
		 - Bar 
	 - Source Data
		 - Qty Purchase Order (QuantityOrdered)
	