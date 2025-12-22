Modeler = Model 2 Test
CDS     = 2CZVBMMXX002_CV
- Average PR approve to PO Received 
	- Numeric 
		PR  Release - PO Received
	- Grafik
		PO Release - PO Received
		PR Approve - PO Release
		PR Created - PR Approved

- Total Document PO Received Latest
	- Numeric
		Total Purchased Document
	- Grafik
		Total Purchased Document
		Group by Invoice Payment Status

- Total Document Invoiced Receipt
	- Numeric
		Total Purchased Document Filter(PO Status = 'PO Received', InvoicePaymentStatus='Invoice Posted & Not Paid') 
	- Grafik
		Po Outstanding, PO Received
- Total Document PO Handling
	Total Purchased Document, Filter PO Status = 'PO Outstanding Partial Received, PO Outstanding'

- PR VS PO Quantity
	- Left Y Axist
		Qty Purchase Request
		Qty Purchase Order
- PR VS PO Value
	-Left y Axist =  Value Purchase Request
	-Righ y Axist = Value Purchase Order
	