# FACILITIES & INFRASTRUCTURE MANAGEMENT MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Facilities & Infrastructure Management Module  
**Role:** Building Maintenance & Utilities - Campus Operations & Asset Preservation Engine  
**Type:** Core Operations Module  
**Dependencies:** Integrates with Accounts, Procurement, HR, Security, Timetable, Events  

**Primary Functions:**
- Building & Room Management - Space allocation, utilization tracking
- Maintenance Request Tracking - Work orders, preventive maintenance
- Preventive Maintenance Scheduling - Equipment servicing, inspections
- Utility Management - Electricity, water, gas consumption tracking
- Housekeeping Management - Cleaning schedules, staff allocation
- Vendor & Contractor Management - Service contracts, performance tracking
- Energy Management - Consumption monitoring, efficiency initiatives
- Space Utilization Analysis - Room occupancy, optimization
- Capital Projects - Renovations, new construction tracking
- Compliance & Safety - Fire safety, building codes, inspections
- Asset Lifecycle Management - Equipment replacement planning
- Environmental Monitoring - Temperature, air quality, lighting

---

## OUTBOUND CONNECTIONS (Facilities & Infrastructure → Other Modules)

### 1. TO ACCOUNTS & FINANCE MODULE

**WHY This Connection Exists:**
All facility-related expenses (utilities, maintenance, repairs, housekeeping) must be recorded in the accounting system. Facilities module generates significant operational expenses that need budget tracking and financial reporting.

**DATA FLOW:**
- **Utility Expenses:**
  - Electricity bills (monthly, consumption in kWh, amount)
  - Water bills (monthly, consumption in kL, amount)
  - Gas bills (for labs, canteen)
  - Internet and telephone bills
  - Sewage and waste management charges
- **Maintenance Expenses:**
  - Repair work invoices
  - Preventive maintenance costs
  - Equipment servicing charges
  - Painting and renovation expenses
  - Plumbing and electrical work
  - HVAC maintenance
  - Pest control services
- **Housekeeping Expenses:**
  - Cleaning staff salaries
  - Cleaning supplies and consumables
  - Waste disposal charges
  - Sanitation services
- **Capital Expenditure:**
  - Building renovations
  - New construction
  - Major equipment purchases
  - Infrastructure upgrades

**TRIGGER EVENT:**
- **Utility Bill Received:** Record expense in accounts
- **Maintenance Work Completed:** Invoice received, record expense
- **Monthly Housekeeping:** Salaries and supplies expense
- **Capital Project Milestone:** Progress payment recorded
- **Vendor Payment Due:** Accounts payable entry

**IMPACT:**
- **Budget Monitoring:**
  - Track facility expenses vs annual budget
  - Identify cost overruns early
  - Optimize spending on utilities and maintenance
- **Financial Reporting:**
  - Facility expenses shown in P&L statement
  - Cost per square foot analysis
  - Department-wise expense allocation
- **Cash Flow Management:**
  - Utility payments are significant monthly outflow
  - Plan for seasonal variations (AC in summer)
  - Capital project payments scheduled

**BUSINESS LOGIC:**
```
FUNCTION record_utility_expense(utility_bill):
  // Validate bill
  IF NOT VALIDATE_UTILITY_BILL(utility_bill):
    RETURN {success: FALSE, error: "Invalid bill data"}
  END IF
  
  // Create expense record
  expense = CREATE_EXPENSE
  expense.category = "UTILITIES_" + utility_bill.utility_type
  expense.vendor = utility_bill.provider
  expense.bill_number = utility_bill.bill_number
  expense.bill_date = utility_bill.date
  expense.consumption = utility_bill.consumption
  expense.unit = utility_bill.unit  // kWh, kL, etc.
  expense.amount = utility_bill.amount
  expense.due_date = utility_bill.due_date
  expense.department = "FACILITIES"
  
  // Send to accounts module
  accounting_entry = {
    module: "FACILITIES",
    transaction_id: expense.id,
    date: utility_bill.date,
    amount: utility_bill.amount,
    category: "EXPENSE_UTILITIES_" + utility_bill.utility_type,
    vendor: utility_bill.provider,
    description: utility_bill.utility_type + " bill for " + utility_bill.period,
    payment_due: utility_bill.due_date
  }
  
  SEND_TO_ACCOUNTS(accounting_entry)
  
  // Track consumption trends
  UPDATE_CONSUMPTION_TRACKER(utility_bill.utility_type, utility_bill.consumption, utility_bill.period)
  
  // Check for unusual consumption
  avg_consumption = GET_AVERAGE_CONSUMPTION(utility_bill.utility_type, last_6_months)
  IF utility_bill.consumption > (avg_consumption * 1.2):
    // 20% increase - alert facilities manager
    SEND_ALERT("facilities.manager@hogwarts.edu.in", 
      "High " + utility_bill.utility_type + " Consumption", 
      "Current: " + utility_bill.consumption + ", Average: " + avg_consumption)
  END IF
  
  LOG_AUDIT("UTILITY_EXPENSE_RECORDED", expense.id, "Type: {utility_bill.utility_type}, Amount: ₹{utility_bill.amount}")
  
  RETURN {success: TRUE, expense_id: expense.id}
END FUNCTION

FUNCTION record_maintenance_expense(work_order, invoice):
  // Create expense record
  expense = CREATE_EXPENSE
  expense.category = "MAINTENANCE_" + work_order.category
  expense.work_order_id = work_order.id
  expense.vendor = invoice.vendor_name
  expense.invoice_number = invoice.number
  expense.invoice_date = invoice.date
  expense.amount_excluding_gst = invoice.amount
  expense.gst_amount = invoice.gst
  expense.total_amount = invoice.total
  expense.description = work_order.description
  expense.location = work_order.location
  
  // Send to accounts module
  accounting_entry = {
    module: "FACILITIES",
    transaction_id: expense.id,
    date: invoice.date,
    amount_excluding_gst: invoice.amount,
    gst_amount: invoice.gst,
    total_amount: invoice.total,
    category: "EXPENSE_MAINTENANCE_" + work_order.category,
    vendor: invoice.vendor_name,
    description: "Maintenance: " + work_order.description
  }
  
  SEND_TO_ACCOUNTS(accounting_entry)
  
  // Update work order status
  work_order.status = "COMPLETED"
  work_order.completion_date = NOW
  work_order.actual_cost = invoice.total
  
  // Track maintenance costs by location/equipment
  UPDATE_MAINTENANCE_TRACKER(work_order.location, work_order.equipment_id, invoice.total)
  
  LOG_AUDIT("MAINTENANCE_EXPENSE_RECORDED", expense.id, "Work Order: {work_order.id}, Amount: ₹{invoice.total}")
  
  RETURN {success: TRUE, expense_id: expense.id}
END FUNCTION
```

**EXAMPLE:**
- **Monthly Utility Expenses (January 2026):**
  - **Electricity:**
    - Consumption: 45,000 kWh
    - Rate: ₹7.50/kWh
    - Fixed Charges: ₹5,000
    - Total: ₹3,42,500
    - Breakdown: Classrooms (40%), Labs (25%), AC (20%), Lighting (10%), Others (5%)
  - **Water:**
    - Consumption: 1,200 kL
    - Rate: ₹20/kL
    - Total: ₹24,000
  - **Internet:**
    - 1 Gbps leased line: ₹15,000
  - **Telephone:**
    - Landlines + mobile: ₹10,000
  - **Total Monthly Utilities:** ₹3,91,500

- **Maintenance Work Order:**
  - Work Order: WO-2026-0234
  - Issue: AC not cooling in Principal's office
  - Category: HVAC Maintenance
  - Vendor: Cool Breeze Services
  - Work Done: Gas refilling, compressor cleaning
  - Invoice: ₹8,500 + GST 18% = ₹10,030
  - Completion: 15-Jan-2026

---

### 2. TO PROCUREMENT & VENDOR MODULE

**WHY This Connection Exists:**
Facilities module requires continuous procurement of supplies (cleaning materials, spare parts, consumables) and vendor services (maintenance contractors, utility providers). Procurement module manages vendor relationships and purchase orders.

**DATA FLOW:**
- **Purchase Requests:**
  - Cleaning supplies (detergents, mops, brooms, toilet paper, hand soap)
  - Maintenance spare parts (electrical fittings, plumbing materials, paint)
  - Equipment (vacuum cleaners, floor polishers, lawn mowers)
  - Consumables (light bulbs, batteries, filters)
- **Vendor Contracts:**
  - Annual Maintenance Contracts (AMC) for elevators, HVAC, fire systems
  - Housekeeping service contracts
  - Pest control contracts
  - Waste management contracts
  - Security services
- **Service Requests:**
  - Emergency repairs
  - Preventive maintenance scheduling
  - Equipment servicing

**TRIGGER EVENT:**
- **Stock Below Threshold:** Auto-generate purchase request
- **Preventive Maintenance Due:** Request vendor scheduling
- **Equipment Breakdown:** Emergency service request
- **Contract Renewal:** Renew AMC contracts
- **New Vendor Required:** Vendor onboarding

**IMPACT:**
- **Timely Procurement:**
  - Cleaning supplies always in stock
  - Spare parts available for quick repairs
  - Vendors scheduled for preventive maintenance
- **Cost Optimization:**
  - Bulk purchasing discounts
  - Vendor performance tracking
  - Contract negotiations based on performance
- **Quality Assurance:**
  - Approved vendor list
  - Quality standards for supplies
  - Service level agreements (SLA) monitoring

**BUSINESS LOGIC:**
```
FUNCTION create_purchase_request(item, quantity, urgency):
  // Check current stock
  current_stock = GET_STOCK_LEVEL(item.id)
  
  IF current_stock < item.minimum_stock:
    // Create purchase request
    pr = CREATE_PURCHASE_REQUEST
    pr.item_id = item.id
    pr.item_name = item.name
    pr.quantity = MAX(quantity, item.reorder_quantity)
    pr.urgency = urgency  // NORMAL, URGENT, EMERGENCY
    pr.requested_by = "FACILITIES"
    pr.purpose = "Facilities maintenance and operations"
    pr.budget_code = "FACILITIES_SUPPLIES"
    
    // Get preferred vendor
    preferred_vendor = GET_PREFERRED_VENDOR(item.category)
    pr.suggested_vendor = preferred_vendor.id
    pr.estimated_cost = item.unit_price * pr.quantity
    
    // Send to procurement module
    SEND_TO_PROCUREMENT(pr)
    
    LOG_AUDIT("PURCHASE_REQUEST_CREATED", pr.id, "Item: {item.name}, Qty: {pr.quantity}")
    
    RETURN {success: TRUE, pr_id: pr.id}
  ELSE:
    RETURN {success: FALSE, message: "Stock sufficient"}
  END IF
END FUNCTION
```

**EXAMPLE:**
- **Cleaning Supplies Purchase Request:**
  - Items:
    - Floor cleaner: 50 liters @ ₹200/L = ₹10,000
    - Toilet cleaner: 30 liters @ ₹150/L = ₹4,500
    - Hand soap: 100 bottles @ ₹80/bottle = ₹8,000
    - Toilet paper: 200 rolls @ ₹30/roll = ₹6,000
    - Garbage bags: 500 pieces @ ₹10/piece = ₹5,000
  - Total: ₹33,500 + GST 18% = ₹39,530
  - Vendor: CleanPro Supplies
  - Delivery: Within 3 days

---

### 3. TO TIMETABLE & SCHEDULING MODULE

**WHY This Connection Exists:**
Facility maintenance and cleaning must be scheduled around class timings to minimize disruption. Timetable module provides class schedules, and Facilities module plans maintenance during free periods or after school hours.

**DATA FLOW:**
- **Class Schedules:**
  - Room-wise timetable
  - Free periods
  - Exam schedules
  - Holiday calendar
- **Maintenance Windows:**
  - Preferred maintenance times
  - Rooms available for maintenance
  - Blackout periods (exams, events)
- **Room Booking:**
  - Temporary room closures for maintenance
  - Alternative room allocation

**TRIGGER EVENT:**
- **Maintenance Scheduled:** Check room availability
- **Emergency Repair:** Notify timetable for room closure
- **Preventive Maintenance:** Schedule during holidays
- **Deep Cleaning:** Schedule during exam periods (rooms empty)

**IMPACT:**
- **Minimal Disruption:**
  - Maintenance during free periods
  - No class interruptions
  - Advance notice for room closures
- **Optimized Scheduling:**
  - Batch maintenance work
  - Utilize holidays for major work
  - Coordinate with exam schedule

**EXAMPLE:**
- **AC Maintenance Scheduling:**
  - Rooms: 50 classrooms with AC
  - Maintenance Required: Annual servicing (2 hours per room)
  - Schedule: Summer vacation (May-June)
  - Coordination: Timetable module confirms no summer classes
  - Execution: 5 technicians, 2 weeks to complete all rooms

---

### 4. TO SECURITY & ACCESS CONTROL MODULE

**WHY This Connection Exists:**
Facilities staff and contractors need access to buildings and rooms for maintenance work. Security module manages access permissions and tracks entry/exit for safety and accountability.

**DATA FLOW:**
- **Access Requests:**
  - Facilities staff ID and access level
  - Contractor temporary access
  - Work order details
  - Access duration
- **Access Logs:**
  - Entry/exit timestamps
  - Rooms accessed
  - Work performed
- **Emergency Access:**
  - After-hours access for emergencies
  - Override permissions

**TRIGGER EVENT:**
- **Work Order Created:** Request access for technician
- **Contractor Onboarding:** Temporary access card issued
- **Emergency Repair:** Emergency access granted
- **Work Completion:** Revoke temporary access

**IMPACT:**
- **Security:**
  - Controlled access to facilities
  - Audit trail of all access
  - Prevent unauthorized entry
- **Accountability:**
  - Track who accessed which rooms
  - Verify work completion
  - Investigate incidents

---

### 5. TO EVENTS & ACTIVITIES MODULE

**WHY This Connection Exists:**
School events require facility setup (stage, seating, AV equipment, decorations) and post-event cleanup. Facilities module coordinates with Events module for venue preparation and restoration.

**DATA FLOW:**
- **Event Requirements:**
  - Venue (auditorium, sports ground, classrooms)
  - Setup requirements (stage, chairs, tables, podium)
  - AV equipment (projector, sound system, microphones)
  - Decorations and signage
  - Catering setup (if applicable)
- **Facility Preparation:**
  - Pre-event cleaning
  - Setup execution
  - Equipment testing
  - Safety checks
- **Post-Event:**
  - Cleanup and restoration
  - Equipment return
  - Damage assessment

**TRIGGER EVENT:**
- **Event Scheduled:** Facility preparation request
- **Event Day:** Setup execution
- **Event Completion:** Cleanup and restoration

**IMPACT:**
- **Event Success:**
  - Well-prepared venues
  - Functional equipment
  - Clean and presentable spaces
- **Facility Protection:**
  - Damage prevention
  - Quick restoration
  - Equipment maintenance

**EXAMPLE:**
- **Annual Day Event (Auditorium):**
  - Date: 15-Mar-2026
  - Capacity: 500 seats
  - Setup (14-Mar):
    - Stage decoration: 4 hours
    - Seating arrangement: 2 hours
    - AV equipment setup: 2 hours
    - Lighting setup: 3 hours
    - Sound check: 1 hour
  - Event Day (15-Mar):
    - Pre-event inspection: 8:00 AM
    - Event: 10:00 AM - 2:00 PM
  - Post-Event (15-Mar):
    - Cleanup: 2:30 PM - 5:00 PM
    - Equipment return: 5:00 PM - 6:00 PM
    - Damage assessment: None
  - Total Facilities Staff: 8 persons

---

### 6. TO HR & TEACHER MANAGEMENT MODULE

**WHY This Connection Exists:**
Housekeeping staff, maintenance technicians, and security guards are employees managed by HR module. Facilities module tracks their work schedules, performance, and training needs.

**DATA FLOW:**
- **Staff Information:**
  - Housekeeping staff (cleaners, janitors)
  - Maintenance technicians (electricians, plumbers, HVAC)
  - Security guards
  - Gardeners and groundskeepers
- **Work Schedules:**
  - Shift timings
  - Leave and attendance
  - Overtime
- **Performance:**
  - Work quality ratings
  - Complaint resolution
  - Training needs

**TRIGGER EVENT:**
- **Staff Hiring:** Onboard new facilities staff
- **Performance Review:** Quarterly evaluation
- **Training Required:** Skill development programs
- **Complaint Received:** Performance issue escalation

**IMPACT:**
- **Workforce Management:**
  - Adequate staffing levels
  - Skill-based task allocation
  - Performance-based incentives
- **Quality Service:**
  - Well-trained staff
  - Consistent service quality
  - Quick issue resolution

---

### 7. TO HEALTH & WELLNESS MODULE

**WHY This Connection Exists:**
Facility conditions (cleanliness, air quality, temperature, lighting) directly impact student and staff health. Facilities module ensures healthy environment, and Health module reports facility-related health issues.

**DATA FLOW:**
- **Environmental Monitoring:**
  - Air quality (CO2 levels, ventilation)
  - Temperature and humidity
  - Lighting levels (lux)
  - Noise levels
  - Water quality
- **Health Incidents:**
  - Sick building syndrome reports
  - Allergy complaints
  - Injury reports (slips, falls)
- **Remediation:**
  - Ventilation improvements
  - Deep cleaning
  - Pest control
  - Water treatment

**TRIGGER EVENT:**
- **Health Complaint:** Investigate facility cause
- **Outbreak:** Deep cleaning and sanitization
- **Inspection:** Environmental audit
- **Seasonal:** AC maintenance before summer, heater check before winter

**IMPACT:**
- **Healthy Environment:**
  - Clean and sanitized spaces
  - Good air quality
  - Comfortable temperature
  - Adequate lighting
- **Preventive Care:**
  - Regular sanitization
  - Pest control
  - Water quality monitoring

---

## INBOUND CONNECTIONS (Other Modules → Facilities & Infrastructure)

### 1. FROM ALL MODULES - FACILITY REQUESTS

**WHY This Connection Exists:**
All modules use facilities (classrooms, labs, offices, auditorium) and may report issues or request maintenance. Facilities module receives requests from across the school.

**DATA FLOW:**
- **Maintenance Requests:**
  - Issue description (AC not working, light bulb fused, water leakage)
  - Location (building, floor, room number)
  - Urgency (low, medium, high, emergency)
  - Reported by (teacher, student, staff)
  - Photos/videos (if applicable)
- **Room Booking:**
  - Purpose (class, meeting, event)
  - Date and time
  - Duration
  - Setup requirements

**TRIGGER EVENT:**
- **Issue Reported:** Create work order
- **Room Needed:** Check availability, book room
- **Emergency:** Immediate response

**IMPACT:**
- **Quick Resolution:**
  - Issues tracked and resolved
  - SLA-based response times
  - Stakeholder satisfaction
- **Resource Optimization:**
  - Room utilization tracking
  - Prevent double-booking
  - Efficient space allocation

**BUSINESS LOGIC:**
```
FUNCTION create_maintenance_request(request):
  // Validate request
  IF NOT VALIDATE_REQUEST(request):
    RETURN {success: FALSE, error: "Invalid request data"}
  END IF
  
  // Determine urgency and SLA
  IF request.urgency == "EMERGENCY":
    sla_response = 1  // 1 hour
    sla_resolution = 4  // 4 hours
  ELSE IF request.urgency == "HIGH":
    sla_response = 4  // 4 hours
    sla_resolution = 24  // 24 hours
  ELSE IF request.urgency == "MEDIUM":
    sla_response = 24  // 24 hours
    sla_resolution = 72  // 72 hours
  ELSE:  // LOW
    sla_response = 48  // 48 hours
    sla_resolution = 168  // 1 week
  END IF
  
  // Create work order
  work_order = CREATE_WORK_ORDER
  work_order.request_id = request.id
  work_order.issue = request.description
  work_order.location = request.location
  work_order.urgency = request.urgency
  work_order.reported_by = request.reporter_id
  work_order.reported_at = NOW
  work_order.sla_response_by = NOW + sla_response * HOURS
  work_order.sla_resolution_by = NOW + sla_resolution * HOURS
  work_order.status = "OPEN"
  
  // Assign to appropriate technician
  IF request.category == "ELECTRICAL":
    work_order.assigned_to = GET_AVAILABLE_ELECTRICIAN()
  ELSE IF request.category == "PLUMBING":
    work_order.assigned_to = GET_AVAILABLE_PLUMBER()
  ELSE IF request.category == "HVAC":
    work_order.assigned_to = GET_AVAILABLE_HVAC_TECHNICIAN()
  ELSE IF request.category == "CARPENTRY":
    work_order.assigned_to = GET_AVAILABLE_CARPENTER()
  ELSE:
    work_order.assigned_to = GET_FACILITIES_SUPERVISOR()
  END IF
  
  // Notify assigned technician
  SEND_NOTIFICATION(work_order.assigned_to.email, 
    "New Work Order: " + work_order.id, 
    "Issue: " + request.description + ", Location: " + request.location + ", SLA: " + sla_response + " hours")
  
  // Notify requester
  SEND_EMAIL(request.reporter_email, 
    "Maintenance Request Received", 
    "Work Order: " + work_order.id + ", Expected Response: " + sla_response + " hours")
  
  LOG_AUDIT("WORK_ORDER_CREATED", work_order.id, "Urgency: {request.urgency}, Location: {request.location}")
  
  RETURN {success: TRUE, work_order_id: work_order.id, sla_response: sla_response}
END FUNCTION
```

**EXAMPLE:**
- **Maintenance Request:**
  - Reporter: Ms. Priya Sharma (Math Teacher)
  - Issue: "Projector not working in Room 301"
  - Location: Academic Block, 3rd Floor, Room 301
  - Urgency: HIGH (class in 2 hours)
  - Reported: 8:30 AM
  - Work Order: WO-2026-0456
  - Assigned: Mr. Ramesh (AV Technician)
  - Response: 9:00 AM (technician arrived)
  - Resolution: 9:30 AM (bulb replaced)
  - Status: CLOSED
  - SLA Met: Yes (response within 4 hours)

---

### 2. FROM ACCOUNTS MODULE - BUDGET ALLOCATION

**WHY This Connection Exists:**
Facilities module receives annual budget allocation from Accounts module and must operate within budget constraints. Budget tracking is critical for financial discipline and planning.

**DATA FLOW:**
- **Budget Allocation:**
  - Utilities budget (electricity, water, gas, internet)
  - Maintenance budget (repairs, preventive maintenance)
  - Housekeeping budget (salaries, supplies)
  - Capital projects budget (renovations, new construction)
  - Contingency fund (emergencies)
- **Budget Utilization:**
  - Monthly spending by category
  - Variance analysis (actual vs budget)
  - Forecast to year-end
  - Budget requests for overruns
- **Budget Reporting:**
  - Monthly budget reports
  - Quarterly variance analysis
  - Annual budget proposal

**TRIGGER EVENT:**
- **Annual Budget:** Receive budget allocation (April)
- **Monthly Close:** Report budget utilization
- **Budget Overrun:** Request additional funds
- **Mid-Year Review:** Budget revision if needed
- **Capital Project:** Special budget approval

**IMPACT:**
- **Financial Discipline:**
  - Spend within allocated budget
  - Prioritize critical work
  - Defer non-urgent work if over budget
  - Seek approvals for overruns
- **Planning:**
  - Forecast annual expenses based on historical data
  - Plan capital projects within budget
  - Optimize resource allocation
  - Negotiate vendor contracts
- **Accountability:**
  - Transparent budget utilization
  - Justify expenses
  - Demonstrate value for money

**BUSINESS LOGIC:**
```
FUNCTION check_budget_availability(expense_category, amount):
  // Get budget allocation for category
  budget = GET_BUDGET_ALLOCATION(expense_category, CURRENT_FISCAL_YEAR)
  
  // Get year-to-date spending
  ytd_spending = GET_YTD_SPENDING(expense_category)
  
  // Calculate remaining budget
  remaining_budget = budget.allocated - ytd_spending
  
  // Check if expense can be accommodated
  IF amount <= remaining_budget:
    RETURN {
      success: TRUE,
      available: TRUE,
      remaining: remaining_budget - amount,
      utilization: ((ytd_spending + amount) / budget.allocated) * 100
    }
  ELSE:
    // Over budget - requires approval
    RETURN {
      success: TRUE,
      available: FALSE,
      shortfall: amount - remaining_budget,
      utilization: ((ytd_spending + amount) / budget.allocated) * 100,
      requires_approval: TRUE
    }
  END IF
END FUNCTION

FUNCTION generate_monthly_budget_report(month):
  report = CREATE_BUDGET_REPORT
  report.month = month
  report.fiscal_year = CURRENT_FISCAL_YEAR
  
  categories = ["UTILITIES", "MAINTENANCE", "HOUSEKEEPING", "CAPITAL_PROJECTS"]
  
  FOR each category IN categories:
    budget_data = {
      category: category,
      allocated: GET_BUDGET_ALLOCATION(category, CURRENT_FISCAL_YEAR),
      spent_this_month: GET_MONTHLY_SPENDING(category, month),
      ytd_spending: GET_YTD_SPENDING(category),
      remaining: 0,
      utilization_percent: 0,
      variance: 0
    }
    
    budget_data.remaining = budget_data.allocated - budget_data.ytd_spending
    budget_data.utilization_percent = (budget_data.ytd_spending / budget_data.allocated) * 100
    budget_data.variance = budget_data.allocated - budget_data.ytd_spending
    
    report.categories.append(budget_data)
  END FOR
  
  // Calculate totals
  report.total_allocated = SUM(report.categories, "allocated")
  report.total_spent = SUM(report.categories, "ytd_spending")
  report.total_remaining = SUM(report.categories, "remaining")
  report.overall_utilization = (report.total_spent / report.total_allocated) * 100
  
  // Generate PDF report
  pdf = GENERATE_PDF_REPORT(report, template="budget_report")
  
  // Send to Accounts and Management
  SEND_EMAIL("accounts@hogwarts.edu.in", "Facilities Budget Report - " + month, pdf)
  SEND_EMAIL("principal@hogwarts.edu.in", "Facilities Budget Report - " + month, pdf)
  
  LOG_AUDIT("BUDGET_REPORT_GENERATED", month, "Total Utilization: {report.overall_utilization}%")
  
  RETURN report
END FUNCTION
```

**EXAMPLE:**
- **Annual Budget (FY 2025-26):**
  - **Utilities:** ₹48,00,000 (₹4,00,000/month)
    - Electricity: ₹36,00,000 (75%)
    - Water: ₹6,00,000 (12.5%)
    - Internet/Telephone: ₹6,00,000 (12.5%)
  - **Maintenance:** ₹36,00,000 (₹3,00,000/month)
    - Preventive Maintenance: ₹15,00,000 (42%)
    - Repairs: ₹18,00,000 (50%)
    - Contingency: ₹3,00,000 (8%)
  - **Housekeeping:** ₹24,00,000 (₹2,00,000/month)
    - Salaries: ₹18,00,000 (75%)
    - Supplies: ₹6,00,000 (25%)
  - **Capital Projects:** ₹50,00,000
    - Library Renovation: ₹20,00,000
    - Playground Upgrade: ₹15,00,000
    - Solar Panel Installation: ₹15,00,000
  - **Total Annual Budget:** ₹1,58,00,000

- **Mid-Year Review (September 2025):**
  - **Utilities:** ₹24,50,000 spent (51% of ₹48L) - On track
  - **Maintenance:** ₹22,00,000 spent (61% of ₹36L) - Over budget due to AC repairs
  - **Housekeeping:** ₹11,50,000 spent (48% of ₹24L) - On track
  - **Capital Projects:** ₹25,00,000 spent (50% of ₹50L) - On track
  - **Action:** Request ₹5L additional for maintenance from contingency fund

---

### 3. FROM STUDENT MANAGEMENT - HOSTEL ROOM ALLOCATION

**WHY This Connection Exists:**
For schools with hostels, Student Management module assigns students to hostel rooms. Facilities module maintains hostel infrastructure, room conditions, and amenities.

**DATA FLOW:**
- **Room Allocation:**
  - Student ID and room number
  - Check-in date
  - Room capacity and occupancy
  - Roommate assignments
- **Room Condition:**
  - Pre-check-in inspection
  - Furniture and fixtures inventory
  - Damage assessment
  - Maintenance requirements
- **Amenities:**
  - Bed, mattress, pillow
  - Study table and chair
  - Wardrobe
  - Fan/AC
  - Lighting
  - Electrical outlets

**TRIGGER EVENT:**
- **Student Check-In:** Room inspection and handover
- **Student Check-Out:** Damage assessment, maintenance
- **Mid-Year:** Room condition inspection
- **Complaint:** Maintenance request from hostel

**IMPACT:**
- **Student Comfort:**
  - Well-maintained rooms
  - Functional amenities
  - Quick issue resolution
- **Asset Protection:**
  - Regular inspections
  - Damage tracking
  - Maintenance scheduling

**EXAMPLE:**
- **Hostel Room Allocation:**
  - Student: Aarav Kumar (Grade 11)
  - Room: H-Block, 3rd Floor, Room 305
  - Check-In: 01-Apr-2025
  - Roommate: Rohan Sharma
  - Pre-Check-In Inspection:
    - Furniture: All items present and functional
    - Electrical: All outlets working, fan operational
    - Plumbing: No leaks, tap working
    - Cleanliness: Room cleaned and sanitized
    - Condition: Good (no damages)
  - Check-Out (31-Mar-2026):
    - Damage: Broken chair leg
    - Charge: ₹500 (deducted from deposit)
    - Maintenance: Chair replaced before next check-in

---

### 4. FROM TRANSPORT MODULE - VEHICLE MAINTENANCE

**WHY This Connection Exists:**
School buses and vehicles require regular maintenance and repairs. Transport module schedules vehicles, Facilities module manages vehicle maintenance.

**DATA FLOW:**
- **Vehicle Information:**
  - Bus number and registration
  - Make, model, year
  - Mileage
  - Service history
- **Maintenance Schedule:**
  - Preventive maintenance (every 5,000 km)
  - Annual fitness certificate
  - Insurance renewal
  - Pollution check
- **Repair Requests:**
  - Breakdown reports
  - Driver complaints
  - Inspection findings

**TRIGGER EVENT:**
- **Mileage Threshold:** Schedule preventive maintenance
- **Breakdown:** Emergency repair
- **Annual:** Fitness certificate renewal
- **Inspection:** Identify maintenance needs

**IMPACT:**
- **Vehicle Reliability:**
  - Reduced breakdowns
  - Safe transportation
  - Compliance with regulations
- **Cost Optimization:**
  - Preventive maintenance cheaper than repairs
  - Extended vehicle life
  - Better fuel efficiency

**EXAMPLE:**
- **Bus Maintenance:**
  - Bus: HR-01-AB-1234 (Route 5)
  - Mileage: 45,000 km
  - Last Service: 40,000 km
  - Due: Preventive maintenance (every 5,000 km)
  - Schedule: Saturday (no school)
  - Work:
    - Engine oil change
    - Oil filter replacement
    - Air filter cleaning
    - Brake inspection
    - Tire rotation
    - Battery check
  - Cost: ₹8,500
  - Duration: 4 hours
  - Next Service: 50,000 km

---

## EMERGENCY RESPONSE PROTOCOLS

### 1. POWER OUTAGE

**Response Procedure:**
1. **Immediate (0-5 minutes):**
   - Check if outage is internal or external (grid failure)
   - Activate backup generator (if available)
   - Notify all staff via PA system
   - Ensure emergency lighting is functional
2. **Short-term (5-30 minutes):**
   - Contact electricity provider for status update
   - Assess critical systems (server room, labs, medical room)
   - Prioritize power restoration (exams, critical equipment)
3. **Extended Outage (>30 minutes):**
   - Adjust class schedule if needed
   - Relocate classes to naturally lit areas
   - Cancel computer labs, AV-dependent classes
   - Communicate with parents if early dismissal needed
4. **Post-Restoration:**
   - Check all equipment for damage
   - Reset systems (computers, servers, HVAC)
   - Document outage duration and impact

**Example:**
- **Incident:** Power outage during exam (10:30 AM)
- **Cause:** Transformer failure (external)
- **Response:**
  - Generator activated within 2 minutes
  - Exam halls prioritized for power
  - Estimated restoration: 2 hours
  - Exams continued without interruption
  - Regular power restored at 12:15 PM
  - No equipment damage reported

---

### 2. WATER LEAKAGE

**Response Procedure:**
1. **Immediate:**
   - Shut off water supply to affected area
   - Place warning signs (wet floor)
   - Evacuate area if necessary
   - Contain water spread (use mops, towels)
2. **Assessment:**
   - Identify source (pipe burst, tank overflow, AC condensate)
   - Assess damage (flooring, furniture, electrical)
   - Determine urgency (minor drip vs major flood)
3. **Repair:**
   - Call plumber for pipe repairs
   - Fix source of leakage
   - Dry affected area (fans, dehumidifiers)
   - Inspect electrical safety
4. **Prevention:**
   - Regular pipe inspections
   - Tank level monitoring
   - AC condensate drain maintenance

**Example:**
- **Incident:** Pipe burst in 2nd floor washroom (2:00 PM)
- **Response:**
  - Water supply shut off immediately
  - Washroom closed, students redirected
  - Plumber arrived within 30 minutes
  - Pipe repaired by 4:00 PM
  - Area dried and cleaned
  - Washroom reopened next morning
  - Damage: Minor (wet floor, no structural damage)

---

### 3. HVAC FAILURE

**Response Procedure:**
1. **Immediate:**
   - Notify affected areas
   - Open windows for ventilation
   - Provide fans if available
   - Assess if classes can continue
2. **Diagnosis:**
   - Check thermostat settings
   - Inspect circuit breakers
   - Check for refrigerant leaks
   - Call HVAC technician
3. **Temporary Solution:**
   - Relocate classes to air-conditioned areas
   - Adjust schedule (early dismissal if extreme heat)
   - Provide water to students
4. **Repair:**
   - Technician diagnosis and repair
   - Test system before resuming normal operations

**Example:**
- **Incident:** AC failure in computer lab during summer (35°C outside)
- **Response:**
  - Lab closed immediately
  - Computer classes relocated to library (AC working)
  - Technician called (arrived in 1 hour)
  - Diagnosis: Compressor failure
  - Repair: Compressor replaced (₹25,000)
  - Duration: 4 hours
  - Lab reopened next day

---

## PREVENTIVE MAINTENANCE SCHEDULES

### Daily Tasks
- **Housekeeping:**
  - Classroom cleaning (sweeping, mopping)
  - Washroom cleaning and sanitization
  - Garbage collection and disposal
  - Common area cleaning
  - Drinking water fountain cleaning
- **Security:**
  - Perimeter check
  - CCTV monitoring
  - Access control verification
- **Utilities:**
  - Water tank level check
  - Generator fuel level check
  - Electrical panel inspection

### Weekly Tasks
- **Housekeeping:**
  - Deep cleaning of washrooms
  - Window cleaning
  - Furniture dusting and polishing
  - Floor scrubbing
- **Maintenance:**
  - Fire extinguisher inspection
  - Emergency exit check
  - Playground equipment inspection
  - Garden maintenance (mowing, watering)

### Monthly Tasks
- **HVAC:**
  - Filter cleaning/replacement
  - Thermostat calibration
  - Condensate drain cleaning
- **Electrical:**
  - Lighting check (replace fused bulbs)
  - Electrical panel inspection
  - UPS battery check
- **Plumbing:**
  - Tap and flush inspection
  - Drain cleaning
  - Water quality testing
- **Safety:**
  - Fire alarm testing
  - Emergency lighting test
  - First aid kit inspection

### Quarterly Tasks
- **HVAC:**
  - Complete system servicing
  - Refrigerant level check
  - Duct cleaning
- **Electrical:**
  - Earthing resistance testing
  - Generator load testing
  - Solar panel cleaning (if installed)
- **Structural:**
  - Building inspection (cracks, leaks)
  - Roof inspection
  - Pest control treatment
- **Equipment:**
  - Elevator servicing
  - Water pump servicing
  - RO plant servicing

### Annual Tasks
- **Compliance:**
  - Fire safety audit
  - Building safety certificate
  - Electrical safety audit
  - Lift fitness certificate
  - Pollution control clearance
- **Major Maintenance:**
  - Building painting (external/internal)
  - Waterproofing (terrace, bathrooms)
  - Deep cleaning (entire campus)
  - Equipment overhaul
- **Upgrades:**
  - Technology upgrades
  - Energy efficiency improvements
  - Infrastructure enhancements

---

## FACILITY MANAGEMENT KPIs

### Operational KPIs
- **Work Order Resolution:**
  - Average resolution time: 48 hours (target: <72 hours)
  - SLA compliance rate: 92% (target: >90%)
  - First-time fix rate: 85% (target: >80%)
  - Backlog: 15 open work orders (target: <20)
- **Preventive Maintenance:**
  - PM completion rate: 95% (target: >90%)
  - Equipment uptime: 98% (target: >95%)
  - PM to reactive ratio: 70:30 (target: 80:20)
- **Response Times:**
  - Emergency: 1 hour (target: <2 hours)
  - High priority: 4 hours (target: <8 hours)
  - Medium priority: 24 hours (target: <48 hours)
  - Low priority: 72 hours (target: <1 week)

### Financial KPIs
- **Budget Performance:**
  - Budget utilization: 95% (target: 90-100%)
  - Cost variance: +5% (target: ±10%)
  - Cost per square foot: ₹85/sq ft/year (benchmark: ₹80-100)
- **Energy Efficiency:**
  - Energy consumption: 12 kWh/sq ft/month (target: <15)
  - Energy cost: ₹90/sq ft/year (target: <₹100)
  - Renewable energy: 15% (target: >20%)
- **Maintenance Costs:**
  - Reactive maintenance: 30% (target: <40%)
  - Preventive maintenance: 70% (target: >60%)
  - Total maintenance cost: 3% of building value (target: 2-4%)

### Quality KPIs
- **Cleanliness:**
  - Cleanliness score: 4.5/5 (target: >4.0)
  - Complaint rate: 2 per month (target: <5)
  - Inspection pass rate: 95% (target: >90%)
- **Safety:**
  - Accident rate: 0.5 per 1000 students (target: <1)
  - Safety audit score: 92% (target: >85%)
  - Fire drill compliance: 100% (target: 100%)
- **Stakeholder Satisfaction:**
  - Teacher satisfaction: 4.2/5 (target: >4.0)
  - Student satisfaction: 4.0/5 (target: >3.8)
  - Parent satisfaction: 4.3/5 (target: >4.0)

### Environmental KPIs
- **Waste Management:**
  - Waste segregation rate: 85% (target: >80%)
  - Recycling rate: 40% (target: >35%)
  - Composting rate: 60% of organic waste (target: >50%)
- **Water Conservation:**
  - Water consumption: 25 L/person/day (target: <30)
  - Rainwater harvesting: 30% of total water (target: >25%)
  - Wastewater recycling: 50% (target: >40%)
- **Green Initiatives:**
  - Tree cover: 25% of campus area (target: >20%)
  - Solar energy: 15% of total energy (target: >20%)
  - Green building rating: LEED Silver (target: LEED Gold)

---

## VENDOR MANAGEMENT

### Vendor Categories
1. **Utility Providers:**
   - Electricity board
   - Water supply department
   - Internet service provider
   - Telephone service provider
2. **Maintenance Contractors:**
   - HVAC maintenance (AMC)
   - Elevator maintenance (AMC)
   - Fire safety systems (AMC)
   - Electrical contractors
   - Plumbing contractors
   - Painting contractors
3. **Housekeeping:**
   - Housekeeping service provider
   - Pest control service
   - Waste management company
   - Garden maintenance
4. **Supplies:**
   - Cleaning supplies vendor
   - Electrical supplies vendor
   - Plumbing supplies vendor
   - Hardware supplies vendor

### Vendor Performance Metrics
- **Quality:** Work quality rating (1-5 scale)
- **Timeliness:** On-time completion rate (%)
- **Cost:** Cost competitiveness (vs market rate)
- **Responsiveness:** Response time to requests
- **Compliance:** Safety and regulatory compliance
- **Documentation:** Proper invoicing and reporting

### Vendor Evaluation (Quarterly)
- **Excellent (4.5-5.0):** Preferred vendor, renew contract
- **Good (3.5-4.4):** Satisfactory, continue with monitoring
- **Average (2.5-3.4):** Performance improvement plan required
- **Poor (<2.5):** Consider vendor replacement

**Example:**
- **Vendor:** Cool Breeze HVAC Services
- **Contract:** Annual Maintenance Contract (₹2,40,000/year)
- **Performance (Q2 2025-26):**
  - Quality: 4.8/5 (excellent work)
  - Timeliness: 95% on-time completion
  - Cost: Competitive (within budget)
  - Responsiveness: Average 2 hours response time
  - Compliance: 100% (all certifications valid)
  - Rating: EXCELLENT
  - Action: Renew contract for next year

---

## SUMMARY

**Total Connections:** 15+ modules interact with Facilities & Infrastructure

**Critical Dependencies:**
- **Accounts & Finance:** Expense recording, budget tracking (largest operational expense after salaries)
- **Procurement:** Supplies and vendor services (continuous procurement)
- **Timetable:** Maintenance scheduling around classes (minimal disruption)
- **Security:** Access control for facilities staff (safety and accountability)
- **Events:** Venue setup and cleanup (event success)
- **HR:** Housekeeping and maintenance staff management (workforce)
- **Health & Wellness:** Environmental health monitoring (student well-being)
- **Student Management:** Hostel room allocation (for residential schools)
- **Transport:** Vehicle maintenance (fleet reliability)

**Data Flow Metrics:**
- **Monthly Utility Expenses:** ₹3-8 lakhs (depending on school size, season, energy efficiency)
  - Electricity: 60-70% of utility costs (AC is major consumer)
  - Water: 10-15% (depends on campus size, hostel)
  - Internet/Telephone: 10-15%
  - Others (gas, sewage): 5-10%
- **Maintenance Work Orders:** 50-200/month (varies by campus age, size)
  - Emergency: 10-20% (immediate response required)
  - High Priority: 20-30% (within 8 hours)
  - Medium Priority: 30-40% (within 48 hours)
  - Low Priority: 20-30% (within 1 week)
- **SLA Compliance:** 85-95% (target: >90%, world-class: >95%)
- **Housekeeping Staff:** 20-100 (depending on campus size, 1 per 5,000-10,000 sq ft)
- **Maintenance Technicians:** 5-20 (electricians, plumbers, HVAC, carpenters, general)
- **Campus Area:** 5-50 acres (urban schools smaller, suburban larger)
- **Building Area:** 50,000-5,00,000 sq ft (built-up area)
- **Rooms:** 50-500 (classrooms, labs, offices, common areas, hostel rooms)

**Integration Complexity:** MEDIUM-HIGH
- Real-time work order tracking across modules (requires robust system)
- Budget monitoring requires accounting integration (daily expense tracking)
- Scheduling requires timetable coordination (avoid class disruption)
- Access control requires security integration (safety and accountability)
- Multi-vendor management complex (contracts, SLAs, performance tracking)
- Emergency response requires communication integration (alerts, notifications)

**Best Practices:**
1. **Preventive Maintenance:** Schedule regular servicing to prevent breakdowns (70% PM, 30% reactive)
2. **SLA Compliance:** Respond to requests within defined timeframes (emergency <2h, high <8h)
3. **Energy Efficiency:** Monitor consumption, optimize usage, invest in efficient equipment (LED, solar)
4. **Vendor Management:** Maintain approved vendor list, track performance, negotiate contracts
5. **Safety First:** Regular safety inspections, fire drills, emergency preparedness (quarterly drills)
6. **Documentation:** Maintain complete records of all work orders, expenses, inspections
7. **Staff Training:** Regular training for housekeeping and maintenance staff (safety, skills)
8. **Technology:** Use CMMS (Computerized Maintenance Management System) for efficiency
9. **Sustainability:** Green initiatives, waste reduction, water conservation (LEED certification)
10. **Stakeholder Communication:** Keep users informed of maintenance schedules, disruptions
11. **Budget Discipline:** Track expenses vs budget, forecast accurately, seek approvals for overruns
12. **Quality Standards:** Define and maintain cleanliness and maintenance standards
13. **Emergency Preparedness:** Documented procedures, trained staff, regular drills
14. **Asset Management:** Track all equipment, plan replacements, maintain lifecycle records
15. **Continuous Improvement:** Regular audits, feedback collection, process optimization

---

# Submodule Breakdown

# FACILITIES & INFRASTRUCTURE MANAGEMENT MODULE - SUBMODULE OVERVIEW

**Module Code:** FAC-021  
**Category:** Operations & Maintenance  
**Priority:** P1  
**Owner:** Facilities Management Team

## Submodule Breakdown

This module is divided into **10 submodules**, each handling a specific aspect of facilities & infrastructure management:

### 1. Building & Room Management
**Code:** FAC-001  
**File:** `01_building_room_management.md`  
**Purpose:** Building and room inventory and allocation  
**Key Features:** Room catalog, space allocation, room booking, occupancy tracking, room utilization reports, floor plans, room capacity management

### 2. Maintenance Request & Work Order System
**Code:** FAC-002  
**File:** `02_maintenance_request_work_order_system.md`  
**Purpose:** Centralized maintenance request and work order management  
**Key Features:** Request submission, work order creation, technician assignment, SLA tracking, status updates, completion verification, request history

### 3. Preventive Maintenance Scheduling
**Code:** FAC-003  
**File:** `03_preventive_maintenance_scheduling.md`  
**Purpose:** Scheduled preventive maintenance for equipment and facilities  
**Key Features:** Maintenance schedules, equipment servicing, inspection checklists, maintenance calendar, automated reminders, compliance tracking

### 4. Utility Management & Monitoring
**Code:** FAC-004  
**File:** `04_utility_management_monitoring.md`  
**Purpose:** Utility consumption tracking and bill management  
**Key Features:** Electricity monitoring, water consumption, gas usage, bill processing, consumption analytics, cost allocation, anomaly detection

### 5. Housekeeping & Cleaning Management
**Code:** FAC-005  
**File:** `05_housekeeping_cleaning_management.md`  
**Purpose:** Housekeeping schedules and cleaning operations  
**Key Features:** Cleaning schedules, zone allocation, staff assignment, quality inspections, supply inventory, deep cleaning schedules, hygiene audits

### 6. Vendor & Contractor Management
**Code:** FAC-006  
**File:** `06_vendor_contractor_management.md`  \n**Purpose:** Vendor contracts and service provider management  
**Key Features:** Vendor database, AMC contracts, service agreements, performance tracking, vendor ratings, contract renewals, payment tracking

### 7. Energy Management & Sustainability
**Code:** FAC-007  
**File:** `07_energy_management_sustainability.md`  
**Purpose:** Energy efficiency and sustainability initiatives  
**Key Features:** Energy audits, consumption optimization, solar panel monitoring, green initiatives, carbon footprint tracking, sustainability reports

### 8. Space Utilization & Optimization
**Code:** FAC-008  
**File:** `08_space_utilization_optimization.md`  
**Purpose:** Space usage analysis and optimization  
**Key Features:** Occupancy tracking, utilization reports, space planning, capacity analysis, room optimization recommendations, heat maps

### 9. Capital Projects & Renovations
**Code:** FAC-009  
**File:** `09_capital_projects_renovations.md`  
**Purpose:** Major construction and renovation project management  
**Key Features:** Project planning, budget tracking, contractor management, milestone tracking, quality inspections, project completion reports

### 10. Safety & Compliance Management
**Code:** FAC-010  
**File:** `10_safety_compliance_management.md`  
**Purpose:** Safety inspections and regulatory compliance  
**Key Features:** Fire safety inspections, building code compliance, safety audits, emergency equipment checks, compliance certificates, inspection reports

## Integration Points

Facilities & Infrastructure Management connects to multiple modules across the Hogwarts ERP system.

## Development Priority

**Phase 1 (Critical):** Submodules 1-2, 4-5  
**Phase 2 (High):** Submodules 3, 6, 10  
**Phase 3 (Medium):** Submodules 7-9  

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 1.1  
**Compliance:** National Building Code, Fire Safety Norms, Environmental Regulations, Occupational Safety Standards, Energy Conservation Building Code

