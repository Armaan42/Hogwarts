# INVENTORY & ASSET MANAGEMENT MODULE - COMPLETE DEPENDENCY ANALYSIS

## 🎯 MODULE OVERVIEW

**Name:** Inventory & Asset Management Module  
**Role:** Asset Tracking, Inventory Control & Resource Optimization Engine  
**Type:** Core Operations & Finance Module  
**Dependencies:** 10+ modules rely on inventory for resource allocation and tracking  

**Primary Functions:**
- Fixed Asset Management (Buildings, Furniture, Equipment)
- Consumable Inventory Tracking (Stationery, Lab Chemicals, Sports Equipment)
- Asset Depreciation Calculation
- Asset Assignment & Allocation (to staff, departments, students)
- Inventory Procurement & Replenishment
- Asset Maintenance Scheduling
- Stock Level Monitoring & Alerts
- Asset Disposal & Write-off Management
- Barcode/RFID Tagging
- Asset Audit & Verification
- Vendor Management for Procurement
- Asset Utilization Analytics
- Insurance & Warranty Tracking
- Asset Transfer Between Locations (Multi-campus)

---

## 📤 OUTBOUND CONNECTIONS (Inventory → Other Modules)

### 1. TO ACCOUNTS & FINANCE MODULE

**WHY This Connection Exists:**
Asset purchases are capital expenditure. Depreciation affects financial statements. Asset disposal impacts profit/loss. Fixed asset register maintained for audit compliance.

**DATA FLOW:**
- **Asset Purchase Transactions:**
  - Purchase date, amount, vendor
  - Asset category (furniture, equipment, vehicles)
  - Payment terms
- **Depreciation Data:**
  - Monthly/annual depreciation
  - Accumulated depreciation
  - Book value
  - Depreciation method (straight-line, WDV)
- **Asset Disposal:**
  - Sale proceeds
  - Book value at disposal
  - Profit/loss on sale
- **Fixed Asset Register:**
  - Complete asset list with values
  - Asset-wise depreciation
  - Net block value

**TRIGGER EVENT:**
- Asset purchased
- Monthly depreciation calculation
- Asset disposed/sold
- Financial year-end closing

**IMPACT:**
- **Computer Lab Equipment Purchase (July 2024):**
  - 40 computers × ₹50,000 = ₹20,00,000
  - Inventory module records asset
  - Accounts module:
    - Debit: Computer Equipment ₹20,00,000
    - Credit: Bank ₹20,00,000
  - Depreciation: 20% p.a. (WDV method)
  - Monthly depreciation: ₹33,333
  - Accounts module records monthly depreciation entry
- **Annual Financial Statements:**
  - Fixed Assets: ₹5 crore (gross block)
  - Accumulated Depreciation: ₹2 crore
  - Net Block: ₹3 crore
  - Data sourced from Inventory module

**BUSINESS LOGIC:**
```
FUNCTION calculate_depreciation(asset, method, rate):
  IF method = "STRAIGHT_LINE":
    annual_depreciation = (asset.purchase_value - asset.salvage_value) / asset.useful_life
  ELSE IF method = "WDV":  // Written Down Value
    annual_depreciation = asset.book_value * (rate / 100)
  END IF
  
  monthly_depreciation = annual_depreciation / 12
  
  // Update asset
  asset.accumulated_depreciation += monthly_depreciation
  asset.book_value = asset.purchase_value - asset.accumulated_depreciation
  
  // Send to accounts
  depreciation_entry = {
    date: TODAY,
    asset: asset,
    amount: monthly_depreciation,
    debit_account: "Depreciation Expense",
    credit_account: "Accumulated Depreciation"
  }
  
  NOTIFY accounts_module(depreciation_entry)
  
  RETURN monthly_depreciation
END FUNCTION
```

**EXAMPLE:**
- **School Bus Depreciation (5 years):**
  - Purchase: April 2020, ₹15,00,000
  - Depreciation: 20% p.a. (WDV)
  - Year 1 (2020-21): ₹3,00,000 (Book value: ₹12,00,000)
  - Year 2 (2021-22): ₹2,40,000 (Book value: ₹9,60,000)
  - Year 3 (2022-23): ₹1,92,000 (Book value: ₹7,68,000)
  - Year 4 (2023-24): ₹1,53,600 (Book value: ₹6,14,400)
  - Year 5 (2024-25): ₹1,22,880 (Book value: ₹4,91,520)
  - All depreciation entries sent to Accounts module monthly

- **Real-world Scenario: Asset Purchase to Financial Reporting:**
  - **July 1, 2024:** Computer lab upgrade approved
  - **July 5:** Purchase requisition: 40 Dell computers
  - **July 10:** PO raised: ₹20,00,000
  - **July 20:** Computers delivered, received by inventory
  - **July 21:** Assets tagged (LAP-201 to LAP-240)
  - **July 22:** Asset registered in system:
    - Category: IT Equipment
    - Useful life: 5 years
    - Depreciation: 20% p.a. (WDV)
    - Salvage value: ₹2,00,000
  - **July 31:** First depreciation entry:
    - Monthly depreciation: ₹33,333
    - Accounts entry: Dr. Depreciation ₹33,333, Cr. Accumulated Depreciation ₹33,333
  - **March 31, 2025:** Financial year-end:
    - Total depreciation (9 months): ₹3,00,000
    - Book value: ₹17,00,000
    - Balance sheet: Fixed Assets ₹17,00,000

---

### 2. TO PROCUREMENT & VENDOR MANAGEMENT MODULE

**WHY This Connection Exists:**
Inventory levels trigger procurement. Purchase requisitions generated when stock falls below reorder level. Vendor selection based on asset category.

**DATA FLOW:**
- **Purchase Requisitions:**
  - Item name, quantity required
  - Current stock level
  - Reorder level, reorder quantity
  - Urgency (normal, urgent, emergency)
- **Vendor Recommendations:**
  - Preferred vendors per category
  - Past purchase history
  - Vendor ratings
- **Purchase Orders:**
  - PO number, date
  - Items, quantities, rates
  - Delivery timeline
  - Payment terms

**TRIGGER EVENT:**
- Stock level below reorder point
- New asset requirement
- Asset replacement needed
- Bulk procurement planning

**IMPACT:**
- **Lab Chemicals Stock Alert:**
  - Hydrochloric Acid: Current stock 2 liters (Reorder level: 5 liters)
  - System generates purchase requisition
  - Procurement module:
    - Identifies vendor: ChemSupply India
    - Creates PO: 10 liters × ₹200 = ₹2,000
    - Expected delivery: 5 days
  - Inventory updated upon receipt

**BUSINESS LOGIC:**
```
FUNCTION check_reorder_level(item):
  IF item.current_stock <= item.reorder_level:
    // Generate purchase requisition
    requisition = CREATE_PURCHASE_REQUISITION
    requisition.item = item
    requisition.quantity = item.reorder_quantity
    requisition.urgency = CALCULATE_URGENCY(item.current_stock, item.consumption_rate)
    requisition.requested_by = "INVENTORY_SYSTEM"
    requisition.date = TODAY
    
    // Get vendor recommendation
    vendor = GET_PREFERRED_VENDOR(item.category)
    requisition.suggested_vendor = vendor
    
    // Send to procurement
    NOTIFY procurement_module(requisition)
    
    // Alert inventory manager
    ALERT inventory_manager("Low stock: {item.name}, Current: {item.current_stock}, Reorder: {item.reorder_level}")
  END IF
END FUNCTION

FUNCTION CALCULATE_URGENCY(current_stock, consumption_rate):
  days_remaining = current_stock / consumption_rate
  
  IF days_remaining < 3:
    RETURN "EMERGENCY"  // Stock-out imminent
  ELSE IF days_remaining < 7:
    RETURN "URGENT"  // Order immediately
  ELSE:
    RETURN "NORMAL"  // Regular procurement
  END IF
END FUNCTION
```

**EXAMPLE:**
- **Stationery Reorder Automation:**
  - **Item:** A4 Paper (500 sheets/ream)
  - **Current Stock:** 10 reams
  - **Reorder Level:** 20 reams
  - **Reorder Quantity:** 50 reams
  - **Consumption Rate:** 5 reams/day
  - **System Action (Automatic):**
    - Detects: Stock (10) < Reorder level (20)
    - Calculates urgency: 10/5 = 2 days remaining → EMERGENCY
    - Generates PR: 50 reams, Urgency: EMERGENCY
    - Vendor: OfficeMax India (preferred vendor)
    - Email to inventory manager: "EMERGENCY: A4 Paper stock critical (2 days remaining)"
    - Procurement: Expedites order, 1-day delivery
  - **Next Day:** 50 reams received, stock updated to 60 reams

---

### 3. TO TIMETABLE & SCHEDULING MODULE

**WHY This Connection Exists:**
Lab equipment, AV equipment, sports equipment allocated based on timetable. Resource availability determines scheduling feasibility.

**DATA FLOW:**
- Lab equipment availability (Science lab, Computer lab)
- AV equipment (projectors, speakers)
- Sports equipment (for PE periods)
- Special room resources

**TRIGGER EVENT:**
- Timetable created
- Lab period scheduled
- Equipment booking requested

**IMPACT:**
- **Science Lab Scheduling:**
  - Grade 9A: Chemistry practical (Wed 10-11 AM)
  - Required: Burners (40), Test tubes (200), Chemicals
  - Inventory confirms availability
  - Timetable approved
- **Equipment Conflict:**
  - Projector needed: Grade 10A (Room 205, 10 AM) + Grade 11B (Room 301, 10 AM)
  - Inventory: Only 1 projector available
  - Timetable module: Reschedules one class

**BUSINESS LOGIC:**
```
FUNCTION check_resource_availability(resource, date, time_slot):
  // Check if resource exists
  IF NOT resource_exists(resource):
    RETURN {available: FALSE, reason: "Resource not found"}
  END IF
  
  // Check if already booked
  bookings = GET_BOOKINGS(resource, date, time_slot)
  IF bookings.count > 0:
    RETURN {available: FALSE, reason: "Already booked", booked_by: bookings[0].class}
  END IF
  
  // Check resource condition
  IF resource.condition = "UNDER_MAINTENANCE":
    RETURN {available: FALSE, reason: "Under maintenance"}
  ELSE IF resource.condition = "DAMAGED":
    RETURN {available: FALSE, reason: "Damaged, needs repair"}
  END IF
  
  // Resource available
  RETURN {available: TRUE}
END FUNCTION
```

**EXAMPLE:**
- **Weekly Lab Schedule (Science Department):**
  - **Monday:**
    - 9-10 AM: Grade 11 Physics (Physics Lab)
    - 11-12 PM: Grade 9 Biology (Biology Lab)
  - **Tuesday:**
    - 10-11 AM: Grade 12 Chemistry (Chemistry Lab)
  - **Wednesday:**
    - 9-11 AM: Grade 10 Physics (Physics Lab) - Double period
    - 11-12 PM: Grade 9 Chemistry (Chemistry Lab)
  - **Inventory Allocation:**
    - Physics Lab: Allocated to Grade 11 (Mon), Grade 10 (Wed)
    - Chemistry Lab: Allocated to Grade 12 (Tue), Grade 9 (Wed)
    - Biology Lab: Allocated to Grade 9 (Mon)
  - **Equipment Check:**
    - All labs have required equipment
    - Chemicals stocked for week
    - Timetable approved

---

### 4. TO LIBRARY MANAGEMENT MODULE

**WHY This Connection Exists:**
Books are inventory items. Book procurement, stock tracking, disposal managed by inventory module.

**DATA FLOW:**
- Book inventory (textbooks, reference books, fiction)
- Book procurement requests
- Book condition tracking
- Book disposal (damaged, outdated)

**TRIGGER EVENT:**
- New books procured
- Books damaged/lost
- Annual stock verification

**IMPACT:**
- **Textbook Procurement (Grade 9, 2024-25):**
  - 180 students enrolled
  - Math textbooks needed: 180
  - Current stock: 150
  - Procurement: 30 additional copies
  - Inventory tracks: Purchase, receipt, issuance

**BUSINESS LOGIC:**
```
FUNCTION process_book_procurement(grade, subject, quantity_needed):
  // Check current stock
  current_stock = GET_BOOK_STOCK(grade, subject)
  
  // Calculate procurement quantity
  IF current_stock < quantity_needed:
    procurement_qty = quantity_needed - current_stock
    // Add buffer (10%)
    procurement_qty = procurement_qty * 1.1
    
    // Create procurement request
    book_details = GET_BOOK_DETAILS(grade, subject)
    pr = CREATE_PURCHASE_REQUISITION
    pr.item = book_details.title
    pr.author = book_details.author
    pr.publisher = book_details.publisher
    pr.isbn = book_details.isbn
    pr.quantity = procurement_qty
    pr.unit_price = book_details.price
    pr.total_amount = procurement_qty * book_details.price
    
    NOTIFY procurement_module(pr)
    NOTIFY library_module("Book procurement initiated: {book_details.title}, Qty: {procurement_qty}")
  ELSE:
    NOTIFY library_module("Sufficient stock available: {current_stock} books")
  END IF
END FUNCTION
```

---

### 5. TO TRANSPORT MANAGEMENT MODULE

**WHY This Connection Exists:**
School buses, vans are fixed assets. Maintenance parts are inventory items. Fuel consumption tracked.

**DATA FLOW:**
- Vehicle asset details (buses, vans)
- Maintenance parts inventory (tires, batteries, oil)
- Fuel consumption tracking
- Vehicle depreciation

**TRIGGER EVENT:**
- Vehicle purchased
- Maintenance scheduled
- Parts replaced
- Fuel refilled

**IMPACT:**
- **Bus Maintenance:**
  - Bus 3 (DL-01-AB-1234): 50,000 km service
  - Parts required: Engine oil (5L), Oil filter (1), Air filter (1)
  - Inventory checks stock, issues parts
  - Maintenance completed, inventory updated

**BUSINESS LOGIC:**
```
FUNCTION process_vehicle_maintenance(vehicle, maintenance_type):
  // Get maintenance checklist
  checklist = GET_MAINTENANCE_CHECKLIST(vehicle.type, maintenance_type)
  
  parts_required = []
  parts_unavailable = []
  
  FOR each item IN checklist.parts:
    stock = GET_STOCK(item.part_id)
    IF stock >= item.quantity:
      parts_required.add({part: item, available: TRUE})
    ELSE:
      parts_unavailable.add({part: item, stock: stock, required: item.quantity})
    END IF
  END FOR
  
  IF parts_unavailable.count > 0:
    // Generate urgent procurement
    FOR each part IN parts_unavailable:
      CREATE_URGENT_PURCHASE_REQUISITION(part)
    END FOR
    RETURN {status: "PENDING", reason: "Parts unavailable", parts: parts_unavailable}
  ELSE:
    // Issue parts
    FOR each part IN parts_required:
      ISSUE_PART(part.part, part.quantity, vehicle.id)
    END FOR
    RETURN {status: "READY", parts_issued: parts_required}
  END IF
END FUNCTION
```

---

### 6. TO HOSTEL & MESS MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel furniture, mess utensils, bedding are inventory items. Mess provisions (food items) tracked.

**DATA FLOW:**
- Hostel furniture (beds, tables, chairs)
- Mess utensils (plates, glasses, cutlery)
- Bedding (mattresses, pillows, sheets)
- Mess provisions (rice, dal, vegetables)

**TRIGGER EVENT:**
- Hostel room furnished
- Mess utensils damaged
- Provisions stock low
- Annual inventory audit

**IMPACT:**
- **Hostel Room Allocation:**
  - Room 101: 3 students
  - Furniture required: 3 beds, 3 tables, 3 chairs, 3 cupboards
  - Inventory confirms availability, assigns assets
  - Asset register updated with room assignment

---

### 7. TO SPORTS & ATHLETICS MODULE

**WHY This Connection Exists:**
Sports equipment (bats, balls, nets, mats) are inventory items. Equipment allocation for practice, matches tracked.

**DATA FLOW:**
- Sports equipment inventory
- Equipment allocation to teams
- Equipment condition tracking
- Replacement needs

**TRIGGER EVENT:**
- Sports season starts
- Equipment damaged
- New sport introduced
- Annual sports day

**IMPACT:**
- **Cricket Season (September-March):**
  - Equipment needed: 10 bats, 50 balls, 2 sets stumps, 5 helmets
  - Inventory allocates equipment to cricket team
  - Coach tracks usage, reports damages
  - Inventory replaces damaged items

---

### 8. TO HR & PAYROLL MODULE

**WHY This Connection Exists:**
Assets assigned to staff (laptops, phones, furniture). Asset handover during joining, return during exit.

**DATA FLOW:**
- Staff asset assignments
- Asset handover records
- Asset return during exit
- Asset condition at return

**TRIGGER EVENT:**
- Staff joins (asset allocation)
- Staff exits (asset return)
- Asset replacement requested

**IMPACT:**
- **New Teacher Joining:**
  - Mr. Sharma joins as Math teacher
  - Assets allocated:
    - Laptop: Dell Latitude (Asset ID: LAP-045)
    - Desk: Wooden desk (Asset ID: DESK-123)
    - Chair: Ergonomic chair (Asset ID: CHAIR-089)
  - Inventory records assignment, Mr. Sharma signs acknowledgment
- **Teacher Exit:**
  - Ms. Gupta resigns
  - Returns: Laptop (good condition), Desk, Chair
  - Inventory verifies, updates status to "Available"

**BUSINESS LOGIC:**
```
FUNCTION assign_asset_to_staff(staff, asset):
  // Check asset availability
  IF asset.status != "AVAILABLE":
    RETURN {error: "Asset not available"}
  END IF
  
  // Create assignment record
  assignment = CREATE_ASSET_ASSIGNMENT
  assignment.asset = asset
  assignment.assigned_to = staff
  assignment.assigned_date = TODAY
  assignment.condition_at_assignment = asset.condition
  assignment.acknowledgment_signed = FALSE
  
  // Update asset status
  asset.status = "ASSIGNED"
  asset.assigned_to = staff
  asset.location = staff.department
  
  // Generate acknowledgment form
  acknowledgment = GENERATE_ASSET_ACKNOWLEDGMENT(staff, asset)
  
  // Notify staff and HR
  NOTIFY staff("Asset assigned: {asset.name}. Please sign acknowledgment.")
  NOTIFY hr_module("Asset {asset.id} assigned to {staff.name}")
  
  RETURN assignment
END FUNCTION

FUNCTION process_staff_exit_asset_return(staff):
  // Get all assets assigned to staff
  assigned_assets = GET_ASSIGNED_ASSETS(staff)
  
  returned_assets = []
  pending_assets = []
  
  FOR each asset IN assigned_assets:
    IF asset.returned = TRUE:
      // Verify condition
      IF asset.condition_at_return = "GOOD":
        asset.status = "AVAILABLE"
      ELSE IF asset.condition_at_return = "DAMAGED":
        asset.status = "UNDER_REPAIR"
        CREATE_REPAIR_REQUEST(asset)
      ELSE IF asset.condition_at_return = "LOST":
        // Charge staff
        CHARGE_STAFF(staff, asset.book_value)
        asset.status = "WRITTEN_OFF"
      END IF
      returned_assets.add(asset)
    ELSE:
      pending_assets.add(asset)
    END IF
  END FOR
  
  IF pending_assets.count > 0:
    ALERT hr_module("Staff {staff.name} exit pending: {pending_assets.count} assets not returned")
    RETURN {status: "PENDING", pending: pending_assets}
  ELSE:
    RETURN {status: "COMPLETE", returned: returned_assets}
  END IF
END FUNCTION
```

---

## 📥 INBOUND CONNECTIONS (Other Modules → Inventory)

### FROM PROCUREMENT MODULE

**WHY:** Procurement completes purchases, inventory receives and records assets.

**DATA RECEIVED:**
- Purchase orders completed
- Items received
- Vendor invoices
- Quality inspection reports

**IMPACT:**
- **Lab Equipment Received:**
  - PO: 40 microscopes
  - Received: 40 units (verified)
  - Inventory: Records receipt, updates stock
  - Assets tagged with barcodes

**TRIGGER:** Goods received, invoice processed

**BUSINESS LOGIC:**
```
FUNCTION receive_goods(po_number, items_received):
  // Verify PO
  po = GET_PURCHASE_ORDER(po_number)
  IF NOT po:
    RETURN {error: "PO not found"}
  END IF
  
  // Quality inspection
  inspection_report = CONDUCT_QUALITY_INSPECTION(items_received)
  
  accepted_items = []
  rejected_items = []
  
  FOR each item IN items_received:
    IF inspection_report[item].status = "PASS":
      // Generate asset tag
      IF item.category = "FIXED_ASSET":
        asset_id = GENERATE_ASSET_ID(item.category)
        ATTACH_BARCODE(item, asset_id)
      END IF
      
      // Update inventory
      UPDATE_STOCK(item, quantity=item.quantity, operation="ADD")
      
      // Record receipt
      receipt = CREATE_GOODS_RECEIPT
      receipt.po_number = po_number
      receipt.item = item
      receipt.quantity = item.quantity
      receipt.date = TODAY
      receipt.inspector = inspection_report.inspector
      
      accepted_items.add(item)
    ELSE:
      rejected_items.add({item: item, reason: inspection_report[item].reason})
    END IF
  END FOR
  
  // Notify procurement
  NOTIFY procurement_module("GRN: {po_number}, Accepted: {accepted_items.count}, Rejected: {rejected_items.count}")
  
  // If rejections, create return note
  IF rejected_items.count > 0:
    CREATE_GOODS_RETURN_NOTE(po_number, rejected_items)
  END IF
  
  RETURN {accepted: accepted_items, rejected: rejected_items}
END FUNCTION
```

---

### FROM FACILITIES MODULE

**WHY:** Facility maintenance requires spare parts, consumables from inventory.

**DATA RECEIVED:**
- Maintenance requests
- Parts required
- Consumables needed (cleaning supplies, electrical items)

**IMPACT:**
- **AC Maintenance:**
  - Room 205 AC servicing
  - Parts needed: Filter (1), Gas refill (1 kg)
  - Inventory issues parts
  - Facilities completes maintenance

**TRIGGER:** Maintenance scheduled, parts requested

---

### FROM ACCOUNTS MODULE

**WHY:** Budget allocation determines procurement limits. Financial year-end requires asset verification.

**DATA RECEIVED:**
- Budget allocation per department
- Procurement limits
- Asset verification requirements
- Audit schedules

**IMPACT:**
- **Annual Budget (2024-25):**
  - IT Department: ₹10 lakh (computers, software)
  - Science Department: ₹5 lakh (lab equipment)
  - Sports: ₹3 lakh (equipment)
  - Inventory tracks spending against budget

**TRIGGER:** Budget approved, financial year-end

---

## 📊 SUMMARY

**Inventory & Asset Management Module connects to 15+ modules**

**Asset Categories:**
- **Fixed Assets:** Buildings, furniture, equipment, vehicles (₹5 crore)
- **Consumables:** Stationery, lab chemicals, sports equipment (₹50 lakh/year)
- **IT Assets:** Computers, projectors, printers (₹1 crore)
- **Books:** Textbooks, reference books, library books (₹30 lakh)

**Inventory Metrics:**
- **Total Assets:** 5,000+ items
- **Asset Value:** ₹6 crore (gross block)
- **Annual Depreciation:** ₹80 lakh
- **Annual Procurement:** ₹1.5 crore
- **Stock Turnover:** 4 times/year (consumables)

**Asset Lifecycle:**
1. **Procurement:** Purchase requisition → PO → Receipt
2. **Tagging:** Barcode/RFID tagging, asset registration
3. **Assignment:** Allocation to department/staff/student
4. **Maintenance:** Scheduled servicing, repairs
5. **Depreciation:** Monthly calculation, accounts update
6. **Disposal:** End-of-life, sale/scrap

**Detailed Asset Breakdown:**
- **IT Equipment:**
  - Computers: 250 units (₹1.25 crore)
  - Projectors: 30 units (₹15 lakh)
  - Printers: 40 units (₹8 lakh)
  - Servers: 5 units (₹25 lakh)
  - Networking equipment: ₹10 lakh
- **Lab Equipment:**
  - Science lab: ₹40 lakh (microscopes, burners, glassware)
  - Computer lab: ₹60 lakh (computers, software)
  - Math lab: ₹5 lakh (models, instruments)
- **Furniture:**
  - Classroom furniture: ₹1 crore (desks, chairs, boards)
  - Office furniture: ₹20 lakh
  - Hostel furniture: ₹30 lakh
- **Vehicles:**
  - Buses: 8 units (₹1.2 crore)
  - Vans: 3 units (₹30 lakh)
- **Sports Equipment:**
  - Cricket: ₹5 lakh
  - Football: ₹3 lakh
  - Basketball: ₹2 lakh
  - Athletics: ₹5 lakh

**Consumables Tracking:**
- **Stationery:**
  - Monthly consumption: ₹50,000
  - Annual: ₹6 lakh
  - Items: Paper, pens, pencils, notebooks, markers
- **Lab Chemicals:**
  - Monthly consumption: ₹30,000
  - Annual: ₹3.6 lakh
  - Items: Acids, bases, salts, indicators
- **Cleaning Supplies:**
  - Monthly consumption: ₹20,000
  - Annual: ₹2.4 lakh
  - Items: Detergents, disinfectants, mops, brooms

**Depreciation Schedule:**
- **Buildings:** 5% p.a. (straight-line)
- **Furniture:** 10% p.a. (WDV)
- **Computers:** 20% p.a. (WDV)
- **Vehicles:** 20% p.a. (WDV)
- **Lab Equipment:** 15% p.a. (WDV)

**Technology Integration:**
- **Barcode Scanning:** Asset tracking, stock verification
- **RFID Tags:** High-value assets (computers, projectors)
- **Mobile App:** Stock checking, asset verification
- **Automated Alerts:** Low stock, maintenance due
- **Cloud Backup:** Daily backup of inventory data
- **Integration:** ERP integration with Accounts, Procurement, HR

**Inventory Management Processes:**
1. **Stock Verification:**
   - Monthly: Consumables (stationery, chemicals)
   - Quarterly: IT equipment, lab equipment
   - Annually: All fixed assets (physical verification)
2. **Reorder Management:**
   - Automated alerts when stock < reorder level
   - Purchase requisitions generated automatically
   - Vendor selection based on past performance
3. **Asset Maintenance:**
   - Preventive maintenance schedules
   - Breakdown maintenance tracking
   - Spare parts inventory management
4. **Asset Disposal:**
   - End-of-life assessment
   - Disposal approval workflow
   - Sale/scrap/donation processing
   - Write-off accounting

**Inventory Analytics:**
- **Stock Turnover Ratio:** 4 times/year (consumables)
- **Asset Utilization:** 85% (IT equipment), 90% (lab equipment)
- **Maintenance Cost:** 5% of asset value annually
- **Stock-out Incidents:** 2 per month (target: 0)
- **Procurement Lead Time:** 7 days (average)
- **Vendor Performance:** 92% on-time delivery

**Audit & Compliance:**
- **Annual Physical Verification:** March (financial year-end)
- **Fixed Asset Register:** Updated monthly
- **Depreciation Schedule:** Compliant with Companies Act
- **Insurance Coverage:** ₹5 crore (all fixed assets)
- **Warranty Tracking:** 500+ items under warranty
- **AMC Tracking:** 200+ items under AMC

**Challenges:**
- **Stock-outs:** Occasional stock-outs of consumables
- **Asset Tracking:** Manual tracking of small items
- **Maintenance Delays:** Spare parts procurement delays
- **Audit Compliance:** Time-consuming physical verification
- **Data Accuracy:** Manual entry errors

**Best Practices:**
- **Automated Reordering:** Reduces stock-outs
- **Barcode Tagging:** Improves asset tracking accuracy
- **Preventive Maintenance:** Reduces breakdown costs
- **Vendor Consolidation:** Better pricing, faster delivery
- **Cloud-based System:** Real-time access, data security
- **Mobile App:** On-the-go stock verification
- **Integration:** Seamless data flow across modules

**Data Freshness:**
- **Real-time:** Stock levels, asset assignments
- **Daily:** Stock movements, consumption
- **Monthly:** Depreciation, utilization reports
- **Annually:** Asset audit, physical verification

**Future Enhancements:**
- **IoT Integration:** Smart tracking of high-value assets
- **AI-based Demand Forecasting:** Predict consumption patterns
- **Blockchain:** Immutable asset transaction records
- **Drone-based Inventory:** Automated stock verification
- **Predictive Maintenance:** AI-based maintenance scheduling

**Real-world Asset Management Scenarios:**

**Scenario 1: Computer Lab Upgrade (Complete Lifecycle)**
- **Phase 1: Planning (June 2024)**
  - Current lab: 40 computers (purchased 2019, 5 years old)
  - Performance issues: Slow, outdated software
  - Decision: Complete replacement
  - Budget approval: ₹20,00,000
  - Vendor selection: Dell India (preferred vendor)
- **Phase 2: Procurement (July 2024)**
  - Purchase requisition: 40 Dell Latitude laptops
  - Specifications: i5 processor, 8GB RAM, 256GB SSD
  - Unit price: ₹50,000
  - Total: ₹20,00,000
  - PO raised: PO-2024-07-001
  - Delivery timeline: 15 days
- **Phase 3: Receipt & Tagging (July 20, 2024)**
  - Goods received: 40 units
  - Quality inspection: All units passed
  - Barcode tagging: LAP-201 to LAP-240
  - Asset registration:
    - Category: IT Equipment
    - Location: Computer Lab, Building A
    - Useful life: 5 years
    - Depreciation: 20% p.a. (WDV)
    - Warranty: 3 years (Dell on-site)
- **Phase 4: Deployment (July 21-25, 2024)**
  - Software installation: Windows 11, Office 365, educational software
  - Network configuration: Connected to school network
  - User accounts created: Student login credentials
  - Lab ready: July 26, 2024
- **Phase 5: Old Asset Disposal (August 2024)**
  - Old computers (40 units): LAP-001 to LAP-040
  - Book value: ₹2,00,000 (after 5 years depreciation)
  - Sale to refurbisher: ₹1,50,000
  - Loss on sale: ₹50,000
  - Accounts entry: Dr. Cash ₹1,50,000, Dr. Loss ₹50,000, Cr. Asset ₹2,00,000
- **Phase 6: Ongoing Management (2024-2029)**
  - Monthly depreciation: ₹33,333
  - Annual maintenance: ₹20,000 (software updates, cleaning)
  - Utilization tracking: 95% (38 hours/week)
  - Condition monitoring: Quarterly checks
  - Year 5 (2029): Replacement cycle begins again

**Scenario 2: Lab Chemicals Stock Management (Monthly Cycle)**
- **Week 1 (January 1-7):**
  - Opening stock: Hydrochloric Acid 8 liters
  - Consumption: 2 liters (Grade 11 Chemistry practicals)
  - Closing stock: 6 liters
  - Status: Above reorder level (5 liters) ✓
- **Week 2 (January 8-14):**
  - Opening stock: 6 liters
  - Consumption: 2 liters (Grade 12 Chemistry practicals)
  - Closing stock: 4 liters
  - **Alert triggered:** Stock below reorder level (5 liters)
  - System action:
    - Email to inventory manager: "Low stock alert: HCl 4L (Reorder: 5L)"
    - Auto-generate PR: 10 liters, Vendor: ChemSupply India
    - Urgency: URGENT (2 days stock remaining at current consumption)
- **Week 3 (January 15-21):**
  - Opening stock: 4 liters
  - Consumption: 1.5 liters
  - Closing stock: 2.5 liters
  - PO raised: PO-2024-01-015, 10 liters × ₹200 = ₹2,000
  - Expected delivery: January 18
- **Week 4 (January 22-28):**
  - Goods received: 10 liters (January 18)
  - Stock updated: 2.5 + 10 = 12.5 liters
  - Consumption: 2 liters
  - Closing stock: 10.5 liters
  - Status: Healthy stock level ✓

**Scenario 3: School Bus Maintenance & Parts Management**
- **Bus Details:**
  - Bus ID: BUS-003
  - Registration: DL-01-AB-1234
  - Model: Tata LP 909
  - Capacity: 40 students
  - Purchase date: April 2020
  - Purchase value: ₹15,00,000
  - Current book value (Year 5): ₹4,91,520
- **Maintenance Schedule:**
  - **10,000 km service (Every 2 months):**
    - Engine oil change: 5 liters
    - Oil filter: 1 unit
    - Air filter: 1 unit
    - Cost: ₹3,000
  - **50,000 km service (Every 10 months):**
    - All 10,000 km items +
    - Brake pads: 1 set
    - Battery check/replacement
    - Tire rotation
    - Cost: ₹8,000
  - **100,000 km service (Every 20 months):**
    - Major overhaul
    - Transmission service
    - Suspension check
    - Cost: ₹25,000
- **Parts Inventory:**
  - Engine oil (20W-40): 50 liters stock, Reorder: 30 liters
  - Oil filters: 10 units stock, Reorder: 5 units
  - Air filters: 8 units stock, Reorder: 4 units
  - Brake pads: 3 sets stock, Reorder: 2 sets
  - Batteries: 2 units stock, Reorder: 1 unit
- **Maintenance Event (March 2024, 50,000 km service):**
  - Service due: March 15, 2024
  - Parts required check (March 10):
    - Engine oil: 5L (Stock: 45L) ✓
    - Oil filter: 1 unit (Stock: 8 units) ✓
    - Air filter: 1 unit (Stock: 7 units) ✓
    - Brake pads: 1 set (Stock: 3 sets) ✓
  - All parts available, service scheduled
  - Service completed: March 15
  - Parts issued, inventory updated:
    - Engine oil: 45L → 40L
    - Oil filter: 8 → 7 units
    - Air filter: 7 → 6 units
    - Brake pads: 3 → 2 sets (triggers reorder alert)
  - Next service: May 2024 (10,000 km)

**Scenario 4: Annual Physical Verification (March 2025)**
- **Preparation (March 1-7):**
  - Verification team: 5 members (Inventory manager + 4 staff)
  - Schedule: 2 weeks (March 10-21)
  - Scope: All fixed assets (5,000+ items)
  - Tools: Barcode scanners, tablets with inventory app
- **Verification Process:**
  - **Week 1 (March 10-14): IT Assets**
    - Computers: 250 units
    - Projectors: 30 units
    - Printers: 40 units
    - Servers: 5 units
    - Process: Scan barcode, verify location, check condition
    - Discrepancies found:
      - 2 laptops missing (LAP-125, LAP-187)
      - 1 projector damaged (PROJ-015)
  - **Week 2 (March 17-21): Furniture, Lab Equipment, Vehicles**
    - Furniture: 2,000+ items
    - Lab equipment: 500+ items
    - Vehicles: 11 units
    - Discrepancies:
      - 5 chairs damaged (CHAIR-045, CHAIR-089, etc.)
      - 1 microscope broken (MIC-023)
- **Reconciliation (March 22-25):**
  - Missing laptops investigation:
    - LAP-125: Found in staff room (location update)
    - LAP-187: Stolen (police complaint filed, insurance claim)
  - Damaged items:
    - Projector PROJ-015: Repair cost ₹8,000 (approved)
    - Chairs: Minor repairs ₹2,000
    - Microscope: Beyond repair, write-off ₹15,000
- **Final Report (March 28):**
  - Total assets verified: 5,000 items
  - Discrepancies: 9 items (0.18%)
  - Missing: 1 item (stolen)
  - Damaged: 8 items
  - Action taken: Repairs, write-offs, insurance claims
  - Verification complete: 99.82% accuracy

**Scenario 5: Hostel Furniture Allocation (New Academic Year)**
- **Hostel Capacity:** 200 students, 50 rooms (4 students/room)
- **Furniture per Room:**
  - Beds: 4 (bunk beds, 2 sets)
  - Study tables: 4
  - Chairs: 4
  - Cupboards: 4
  - Bookshelves: 2
- **New Admissions (July 2024):** 60 new hostel students
- **Furniture Requirement:**
  - New rooms needed: 15 rooms (60 students ÷ 4)
  - Beds: 15 × 4 = 60 units
  - Tables: 60 units
  - Chairs: 60 units
  - Cupboards: 60 units
  - Bookshelves: 30 units
- **Inventory Check (June 2024):**
  - Available stock:
    - Beds: 40 units (Shortage: 20 units)
    - Tables: 65 units ✓
    - Chairs: 70 units ✓
    - Cupboards: 55 units (Shortage: 5 units)
    - Bookshelves: 35 units ✓
- **Procurement (June-July 2024):**
  - Beds: 20 units × ₹5,000 = ₹1,00,000
  - Cupboards: 5 units × ₹3,000 = ₹15,000
  - Total: ₹1,15,000
  - Vendor: FurnitureMart India
  - Delivery: July 10, 2024
- **Allocation (July 15-20, 2024):**
  - Rooms 51-65 furnished
  - Assets tagged: BED-401 to BED-460, etc.
  - Room-wise asset register updated
  - Students assigned: July 21, 2024

**Asset Utilization Analytics:**

**IT Equipment Utilization (2024-25):**
- **Computer Lab:**
  - Total computers: 40 units
  - Operating hours: 8 AM - 5 PM (9 hours/day)
  - Utilization: 38 hours/week (95%)
  - Peak hours: 10 AM - 12 PM, 2 PM - 4 PM
  - Idle time: 5% (maintenance, holidays)
- **Projectors:**
  - Total projectors: 30 units
  - Classrooms: 25, Auditorium: 3, Conference room: 2
  - Average usage: 4 hours/day per projector
  - Utilization: 50% (4/8 hours)
  - Recommendation: Reduce to 25 projectors, save ₹5 lakh
- **Printers:**
  - Total printers: 40 units
  - Departments: 10 (4 printers each)
  - Average usage: 500 pages/day per printer
  - Utilization: 60%
  - Recommendation: Centralize printing, reduce to 30 printers

**Maintenance Cost Analysis (2024-25):**
- **Total Asset Value:** ₹6 crore
- **Annual Maintenance Budget:** ₹30 lakh (5% of asset value)
- **Breakdown:**
  - IT equipment: ₹10 lakh (AMC, repairs)
  - Vehicles: ₹8 lakh (servicing, parts)
  - Lab equipment: ₹5 lakh (calibration, repairs)
  - Furniture: ₹3 lakh (repairs, refurbishment)
  - Building infrastructure: ₹4 lakh (HVAC, electrical)
- **Cost per Category:**
  - Preventive maintenance: ₹20 lakh (67%)
  - Breakdown maintenance: ₹8 lakh (27%)
  - Emergency repairs: ₹2 lakh (6%)

**Vendor Performance Scorecard (2024-25):**
- **Top 5 Vendors:**
  1. **Dell India (IT Equipment):**
     - Orders: 15 (₹50 lakh)
     - On-time delivery: 95%
     - Quality: 98%
     - Rating: 4.7/5
  2. **ChemSupply India (Lab Chemicals):**
     - Orders: 50 (₹10 lakh)
     - On-time delivery: 92%
     - Quality: 100%
     - Rating: 4.5/5
  3. **FurnitureMart India (Furniture):**
     - Orders: 8 (₹15 lakh)
     - On-time delivery: 88%
     - Quality: 95%
     - Rating: 4.3/5
  4. **Tata Motors (Vehicle Parts):**
     - Orders: 30 (₹8 lakh)
     - On-time delivery: 90%
     - Quality: 97%
     - Rating: 4.4/5
  5. **OfficeMax India (Stationery):**
     - Orders: 100 (₹6 lakh)
     - On-time delivery: 85%
     - Quality: 92%
     - Rating: 4.0/5

**Stock-out Incident Analysis (2024-25):**
- **Total Incidents:** 24 (2 per month)
- **Top 5 Items:**
  1. A4 Paper: 6 incidents (25%)
  2. Whiteboard markers: 4 incidents (17%)
  3. Lab chemicals (HCl): 3 incidents (12%)
  4. Printer toner: 3 incidents (12%)
  5. Sports equipment (balls): 2 incidents (8%)
- **Root Causes:**
  - Inaccurate consumption forecasting: 40%
  - Vendor delivery delays: 30%
  - Unexpected demand spikes: 20%
  - Manual reorder process: 10%
- **Corrective Actions:**
  - Implement automated reordering: Reduce incidents by 50%
  - Increase safety stock: 20% buffer for critical items
  - Vendor performance tracking: Penalize delays
  - Demand forecasting: Use historical data + AI

**Insurance & Risk Management:**
- **Total Insurance Coverage:** ₹5 crore (all fixed assets)
- **Premium:** ₹5 lakh/year (1% of coverage)
- **Claims (2024-25):**
  - Laptop stolen: ₹50,000 (claimed, approved)
  - Bus accident damage: ₹2,00,000 (claimed, pending)
  - Fire damage (lab equipment): ₹0 (no incidents)
- **Risk Mitigation:**
  - CCTV surveillance: 50 cameras
  - Security guards: 24/7 monitoring
  - Fire safety: Extinguishers, smoke detectors
  - Asset tagging: Barcode/RFID for tracking

This module is the **"resource optimization engine"** - tracking every asset from procurement to disposal, ensuring optimal utilization, preventing stock-outs, maintaining accurate financial records for audit compliance, and enabling data-driven decision-making for resource allocation and procurement planning through comprehensive lifecycle management, real-time monitoring, predictive analytics, and vendor performance optimization.


