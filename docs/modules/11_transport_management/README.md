# TRANSPORT MANAGEMENT MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Transport Management Module 
**Role:** Student Transportation & Safety Hub - Fleet & Route Operations 
**Type:** Critical Operational & Safety Module 
**Dependencies:** 15+ modules rely on transport data for student safety and logistics 

**Primary Functions:**
- Bus Route Planning & Optimization
- Student Transport Enrollment & Assignment
- GPS Tracking & Real-time Monitoring
- Driver & Vehicle Management
- Pickup/Drop Schedule Management
- RFID-based Boarding/Deboarding Tracking
- Parent Notifications (bus location, delays, alerts)
- Transport Fee Calculation & Collection
- Maintenance Scheduling & Vehicle Health
- Emergency Response & Incident Management
- Route Efficiency Analytics
- Fuel Consumption & Cost Tracking

---

## OUTBOUND CONNECTIONS (Transport Management → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
Student residential address determines bus route assignment. Transport enrollment status affects student services. Pickup/drop logs provide safety tracking for parents. Transport-related incidents must be linked to student records.

**DATA FLOW:**
- **Route Assignment:**
 - Student address (GPS coordinates, landmark)
 - Distance from school
 - Route number assigned
 - Pickup point location
 - Pickup time (calculated based on route)
 - Drop point location
 - Expected drop time
- **Transport Enrollment Status:**
 - ENROLLED (using school transport)
 - NOT_ENROLLED (self-transport)
 - TEMPORARY (occasional usage, per-trip payment)
 - SUSPENDED (non-payment, service suspended)
- **Safety Tracking:**
 - Daily boarding time (RFID scan)
 - Daily deboarding time at school
 - Evening boarding time (school to home)
 - Evening deboarding time at home
 - GPS location logs
- **Transport Incidents:**
 - Bus breakdowns (student affected)
 - Accidents (student involved)
 - Misbehavior on bus (disciplinary)
 - Lost items on bus

**TRIGGER EVENT:**
- **New Admission with Transport:** Route assignment based on address
- **Address Change:** Route reassignment if moved to different area
- **Transport Enrollment:** Student added to bus roster
- **Transport Withdrawal:** Student removed from bus roster, pro-rata refund
- **Daily Boarding/Deboarding:** RFID scans logged to student record

**IMPACT:**
- **Route Assignment:**
 - Aarav lives in Sector 14, Dwarka (12 km from school)
 - System assigns: Bus Route 3 (covers Sector 12-16)
 - Pickup point: Sector 14 Main Gate
 - Pickup time: 7:25 AM (calculated based on route sequence)
 - Drop time (evening): 3:45 PM
 - Parent receives: "Aarav assigned to Bus Route 3, Driver: Mr. Sharma (98xxx), Pickup: 7:25 AM"
- **Safety Tracking:**
 - **Morning Journey:**
 - 7:27 AM: Aarav boards bus (RFID scan), parent SMS: "Aarav boarded Bus 3"
 - 8:15 AM: Bus reaches school, Aarav deboards (RFID scan), parent SMS: "Aarav reached school"
 - **Evening Journey:**
 - 3:05 PM: Aarav boards bus (RFID scan), parent SMS: "Aarav boarded bus for home"
 - 3:50 PM: Aarav deboards at Sector 14 (RFID scan), parent SMS: "Aarav reached home stop"
 - **Complete tracking:** Parents know child's journey from home to school and back
- **Service Suspension:**
 - Rohan's transport fee overdue 65 days (₹12,000 pending)
 - System suspends transport access
 - RFID card blocked, cannot board bus
 - Parent SMS: "Transport suspended due to non-payment. Clear ₹12,000 to restore."
 - Payment made → RFID reactivated within 1 hour
- **Address Change:**
 - Priya's family relocates from Sector 14 to Sector 22 (different area)
 - Old route: Bus Route 3 (Sector 12-16)
 - New route: Bus Route 7 (Sector 20-24)
 - System reassigns automatically
 - Transport fee adjusted (Route 7 is 18 km, higher fee)
 - Parent notified: "Route changed to Bus Route 7, new pickup time: 7:10 AM"

**BUSINESS LOGIC:**
```
FUNCTION assign_bus_route(student):
 address = student.residential_address
 gps_coordinates = GEOCODE(address)
 distance_from_school = CALCULATE_DISTANCE(gps_coordinates, school_location)
 
 // Find best route based on proximity
 all_routes = GET bus_routes ORDER BY distance_from(gps_coordinates)
 
 FOR each route IN all_routes:
 IF route.covers_area(gps_coordinates) AND route.has_capacity:
 // Assign to this route
 student.transport_route = route
 student.transport_distance = distance_from_school
 
 // Find nearest pickup point on route
 pickup_point = route.find_nearest_pickup_point(gps_coordinates)
 student.pickup_point = pickup_point
 
 // Calculate pickup time based on route sequence
 student.pickup_time = route.calculate_pickup_time(pickup_point)
 
 // Calculate transport fee based on distance
 student.transport_fee = CALCULATE_FEE(distance_from_school)
 
 // Add to route roster
 route.add_student(student)
 
 // Notify parent
 SEND SMS(parent, "Assigned to {route.name}, Driver: {route.driver.name}, Pickup: {student.pickup_time}")
 
 RETURN route
 END IF
 END FOR
 
 RETURN "Error: No route available for this address"
END FUNCTION

FUNCTION track_boarding(student, bus, rfid_scan_time, location):
 boarding_log = CREATE BoardingLog
 boarding_log.student = student
 boarding_log.bus = bus
 boarding_log.scan_time = rfid_scan_time
 boarding_log.location = location
 boarding_log.type = "BOARDING"
 
 // Update student status
 student.current_location = "ON_BUS"
 student.current_bus = bus
 
 // Calculate expected arrival
 IF location = student.pickup_point:
 // Morning journey
 expected_arrival = rfid_scan_time + bus.travel_time_to_school
 boarding_log.expected_arrival = expected_arrival
 boarding_log.journey_type = "HOME_TO_SCHOOL"
 ELSE:
 // Evening journey
 expected_arrival = rfid_scan_time + bus.travel_time_to_home(student.pickup_point)
 boarding_log.expected_arrival = expected_arrival
 boarding_log.journey_type = "SCHOOL_TO_HOME"
 END IF
 
 // Notify parent
 SEND SMS(parent, "{student.name} boarded {bus.route_name} at {rfid_scan_time}")
 
 // Update parent app with live GPS tracking
 ENABLE_GPS_TRACKING(parent_app, bus.gps_device)
 
 RETURN boarding_log
END FUNCTION

FUNCTION track_deboarding(student, bus, rfid_scan_time, location):
 deboarding_log = CREATE DeboardingLog
 deboarding_log.student = student
 deboarding_log.bus = bus
 deboarding_log.scan_time = rfid_scan_time
 deboarding_log.location = location
 deboarding_log.type = "DEBOARDING"
 
 // Get boarding log for this journey
 boarding_log = GET boarding_log WHERE student = student AND date = TODAY AND type = "BOARDING"
 
 // Calculate journey duration
 journey_duration = rfid_scan_time - boarding_log.scan_time
 deboarding_log.journey_duration = journey_duration
 
 // Update student status
 IF location = school_location:
 student.current_location = "AT_SCHOOL"
 deboarding_log.journey_type = "HOME_TO_SCHOOL"
 ELSE:
 student.current_location = "AT_HOME"
 deboarding_log.journey_type = "SCHOOL_TO_HOME"
 END IF
 
 student.current_bus = NULL
 
 // Notify parent
 IF location = school_location:
 SEND SMS(parent, "{student.name} reached school at {rfid_scan_time}")
 ELSE:
 SEND SMS(parent, "{student.name} reached home stop at {rfid_scan_time}")
 END IF
 
 // Disable GPS tracking
 DISABLE_GPS_TRACKING(parent_app)
 
 RETURN deboarding_log
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Daily Transport Journey:**
 - **Morning (Home to School):**
 - 7:10 AM: Kavya walks to Sector 22 Main Gate (pickup point)
 - 7:15 AM: Bus Route 7 arrives, Kavya boards, RFID card scanned
 - System logs: "Kavya boarded Bus 7 at 7:15 AM, Sector 22 Main Gate"
 - Parent SMS: "Kavya boarded Bus 7 at 7:15 AM"
 - Parent app shows: Live GPS location of Bus 7
 - 8:05 AM: Bus reaches school, Kavya deboards, RFID scanned at school gate
 - System logs: "Kavya deboarded at school at 8:05 AM"
 - Parent SMS: "Kavya reached school at 8:05 AM"
 - Journey duration: 50 minutes
 - **Evening (School to Home):**
 - 3:05 PM: School dismissal, Kavya boards Bus 7, RFID scanned
 - Parent SMS: "Kavya boarded bus for home at 3:05 PM"
 - 3:55 PM: Bus reaches Sector 22 Main Gate, Kavya deboards, RFID scanned
 - Parent SMS: "Kavya reached home stop at 3:55 PM"
 - Journey duration: 50 minutes
 - **Complete daily tracking:** 4 RFID scans, 4 parent notifications, GPS tracking during journeys

---

### 2. TO PARENT ENGAGEMENT PORTAL

**WHY This Connection Exists:**
Parents need real-time bus location tracking for child safety. Transport enrollment, route details, and fee information must be accessible. Delay notifications and emergency alerts are critical for parent peace of mind.

**DATA FLOW:**
- **Transport Dashboard:**
 - Route assignment details
 - Bus number and driver information
 - Pickup/drop points and timings
 - Transport fee status
 - Boarding/deboarding history
- **Live GPS Tracking:**
 - Real-time bus location on map
 - Estimated time of arrival (ETA)
 - Bus speed and route progress
 - Geofencing alerts (bus entered/exited area)
- **Notifications:**
 - Bus approaching pickup point (10 mins before)
 - Child boarded bus
 - Child reached school
 - Bus delayed (traffic, breakdown)
 - Route change or cancellation
 - Emergency alerts

**TRIGGER EVENT:**
- **Parent Login:** Transport dashboard displayed
- **Bus Approaching:** Notification sent 10 minutes before pickup
- **RFID Scan:** Boarding/deboarding notification
- **Bus Delay:** Alert sent with reason and new ETA
- **Emergency:** Immediate alert (accident, breakdown)

**IMPACT:**
- **Live GPS Tracking:**
 - Mr. Sharma opens parent app at 7:20 AM
 - Sees Bus Route 3 location on map (currently at Sector 12)
 - ETA to Aarav's pickup point (Sector 14): 5 minutes
 - Aarav ready at gate by 7:25 AM
 - Bus arrives 7:26 AM, Aarav boards
 - Mr. Sharma sees notification: "Aarav boarded Bus 3 at 7:26 AM"
 - Tracks bus journey to school on map
 - 8:15 AM: "Aarav reached school"
- **Delay Notification:**
 - Bus Route 5 stuck in traffic jam
 - Driver updates system: "Delay 30 mins due to traffic"
 - All 45 parents on Route 5 receive SMS: "Bus delayed 30 mins due to traffic. New ETA: 8:00 AM"
 - Parents adjust morning schedule accordingly
- **Emergency Alert:**
 - Bus Route 7 has minor accident (no injuries)
 - Driver presses emergency button
 - All parents receive immediate alert: "EMERGENCY: Bus Route 7 involved in minor accident. All students safe. Alternate bus arranged."
 - School sends follow-up: "Alternate bus arriving in 15 mins"
 - Parents relieved, informed throughout

**BUSINESS LOGIC:**
```
FUNCTION send_bus_approaching_notification(bus, pickup_point):
 // Get students at this pickup point
 students = GET students WHERE pickup_point = pickup_point AND bus_route = bus.route
 
 // Calculate ETA
 current_location = bus.gps_location
 distance_to_pickup = CALCULATE_DISTANCE(current_location, pickup_point.location)
 eta = distance_to_pickup / bus.average_speed
 
 IF eta <= 10 minutes:
 FOR each student IN students:
 parent_contacts = GET student.parent_contacts
 
 message = "Bus {bus.route_name} approaching {pickup_point.name}. ETA: {eta} minutes."
 
 SEND SMS(parent_contacts.mobile, message)
 SEND APP_NOTIFICATION(parent_contacts.app, message)
 END FOR
 END IF
END FUNCTION

FUNCTION notify_bus_delay(bus, delay_minutes, reason):
 students = GET students WHERE bus_route = bus.route
 
 FOR each student IN students:
 parent_contacts = GET student.parent_contacts
 
 // Calculate new ETA
 original_eta = student.expected_pickup_time
 new_eta = original_eta + delay_minutes
 
 message = "Bus {bus.route_name} delayed {delay_minutes} mins due to {reason}. New ETA: {new_eta}"
 
 SEND SMS(parent_contacts.mobile, message)
 SEND APP_NOTIFICATION(parent_contacts.app, message)
 END FOR
 
 // Log delay
 CREATE DelayLog(bus, delay_minutes, reason, timestamp=NOW)
END FUNCTION

FUNCTION handle_emergency_alert(bus, emergency_type, details):
 students = GET students WHERE bus_route = bus.route AND current_location = "ON_BUS"
 
 FOR each student IN students:
 parent_contacts = GET student.parent_contacts
 
 message = "EMERGENCY: Bus {bus.route_name} - {emergency_type}. {details}. School contacting you shortly."
 
 // Multi-channel urgent alert
 SEND SMS(parent_contacts.mobile, message) PRIORITY=URGENT
 SEND EMAIL(parent_contacts.email, message) PRIORITY=URGENT
 SEND APP_NOTIFICATION(parent_contacts.app, message) PRIORITY=URGENT
 MAKE_CALL(parent_contacts.mobile, automated_message) // Automated call
 END FOR
 
 // Alert school administration
 ALERT principal, transport_manager, security_head
 
 // Create incident report
 CREATE EmergencyIncident(bus, emergency_type, details, students_affected=students.count)
 
 // Dispatch help
 IF emergency_type = "ACCIDENT":
 CALL ambulance, police
 ELSE IF emergency_type = "BREAKDOWN":
 DISPATCH backup_bus
 END IF
END FUNCTION
```

**EXAMPLE:**
- **Mrs. Sharma's Morning Routine with Parent App:**
 - **7:10 AM:** Opens parent app, sees Priya's transport dashboard
 - Route: Bus Route 3
 - Driver: Mr. Verma (98xxx)
 - Pickup Point: Sector 14 Main Gate
 - Scheduled Pickup: 7:25 AM
 - Bus current location: Sector 12 (on map)
 - ETA: 8 minutes
 - **7:17 AM:** Notification: "Bus approaching in 8 minutes"
 - **7:20 AM:** Priya walks to pickup point
 - **7:26 AM:** Notification: "Priya boarded Bus 3 at 7:26 AM"
 - **7:26-8:15 AM:** Mrs. Sharma tracks bus on map (live GPS)
 - Sees bus moving towards school
 - Current location updates every 30 seconds
 - **8:16 AM:** Notification: "Priya reached school at 8:16 AM"
 - **Mrs. Sharma:** Relieved, knows Priya safe at school

---

### 3. TO FEE MANAGEMENT MODULE

**WHY This Connection Exists:**
Transport fee is separate from tuition fee, calculated based on distance/route. Non-payment leads to transport service suspension. Mid-year enrollment/withdrawal requires pro-rata fee calculation.

**DATA FLOW:**
- **Transport Fee Calculation:**
 - Route-based fee structure
 - Distance-based fee tiers
 - Annual fee amount
 - Quarterly/monthly installments
- **Enrollment Status:**
 - Enrolled → Fee charged
 - Withdrawn → Pro-rata refund
 - Suspended → Fee still charged (service suspended, not enrollment)
- **Payment Status:**
 - Paid → Transport access active
 - Overdue 30 days → Warning
 - Overdue 60 days → Service suspended

**TRIGGER EVENT:**
- **Transport Enrollment:** Fee added to student invoice
- **Transport Withdrawal:** Pro-rata refund calculated
- **Payment Overdue 60 days:** Transport access suspended
- **Payment Received:** Transport access restored
- **Route Change:** Fee adjusted if new route has different pricing

**IMPACT:**
- **Route-based Fee Structure:**
 - Route 1 (0-5 km): ₹12,000/year
 - Route 2 (5-10 km): ₹18,000/year
 - Route 3 (10-15 km): ₹24,000/year
 - Route 4 (15+ km): ₹30,000/year
- **Fee Calculation Example:**
 - Aarav lives 12 km from school → Route 3
 - Annual transport fee: ₹24,000
 - Quarterly: ₹6,000 each (April, July, Oct, Jan)
- **Service Suspension:**
 - Rohan's transport fee: ₹18,000/year (Route 2)
 - Q1 (April): ₹4,500 paid 
 - Q2 (July): ₹4,500 paid 
 - Q3 (October): Not paid, overdue 35 days
 - System warning: "Transport fee overdue, pay within 25 days to avoid suspension"
 - Day 60: Still unpaid
 - Transport suspended, RFID card blocked
 - Rohan cannot board bus on Day 61
 - Parent pays ₹9,000 (Q3 + Q4) on Day 62
 - RFID reactivated same day, can board from next day
- **Mid-Year Withdrawal:**
 - Priya withdraws from transport in October (6 months used)
 - Transport fee: ₹24,000/year
 - Used: 6 months = ₹12,000
 - Refund: ₹12,000 (credited to fee account within 15 days)

**BUSINESS LOGIC:**
```
FUNCTION calculate_transport_fee(student):
 IF student.transport_enrolled = FALSE:
 RETURN 0
 END IF
 
 route = student.transport_route
 distance = student.transport_distance
 
 // Distance-based fee tiers
 IF distance <= 5:
 annual_fee = 12000
 ELSE IF distance <= 10:
 annual_fee = 18000
 ELSE IF distance <= 15:
 annual_fee = 24000
 ELSE:
 annual_fee = 30000
 END IF
 
 // Pro-rata if enrolled mid-year
 IF student.transport_enrollment_month > 4: // After April
 months_remaining = 12 - student.transport_enrollment_month
 annual_fee = (annual_fee / 12) * months_remaining
 END IF
 
 RETURN annual_fee
END FUNCTION

FUNCTION suspend_transport_access(student, reason):
 student.transport_access = "SUSPENDED"
 student.transport_suspension_date = TODAY
 student.transport_suspension_reason = reason
 
 // Block RFID card
 BLOCK_RFID_CARD(student.rfid_card)
 
 // Notify stakeholders
 SEND SMS(parent, "Transport suspended: {reason}. Clear dues to restore access.")
 NOTIFY bus_driver("Student {student.name} suspended, do not allow boarding")
 NOTIFY transport_manager("Transport suspended: {student.name} - {reason}")
 
 LOG transport_suspension(student, reason)
END FUNCTION

FUNCTION restore_transport_access(student):
 student.transport_access = "ACTIVE"
 student.transport_restoration_date = TODAY
 
 // Unblock RFID card
 UNBLOCK_RFID_CARD(student.rfid_card)
 
 // Notify stakeholders
 SEND SMS(parent, "Transport access restored. {student.name} can board bus from tomorrow.")
 NOTIFY bus_driver("Student {student.name} access restored")
 
 LOG transport_restoration(student)
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Transport Fee Journey:**
 - **Admission (April):** Enrolls in transport, Route 3 (12 km)
 - **Annual Fee:** ₹24,000
 - **Payment Plan:** Quarterly (₹6,000 each)
 - **Q1 (April):** ₹6,000 paid 
 - **Q2 (July):** ₹6,000 paid 
 - **Q3 (October):** ₹6,000 paid 
 - **Q4 (January):** ₹6,000 paid 
 - **Total Paid:** ₹24,000 
 - **Service:** Active throughout year, no interruptions

---

### 4. TO ATTENDANCE MANAGEMENT MODULE

**WHY This Connection Exists:**
Bus boarding logs validate school attendance. Students who boarded bus but marked absent in school need investigation. Late arrivals due to bus delays should be excused.

**DATA FLOW:**
- **Bus Boarding Logs:**
 - Student boarded bus (RFID scan at pickup point)
 - Student deboarded at school (RFID scan at school gate)
 - Timestamps for both
- **Attendance Reconciliation:**
 - Bus attendance vs school attendance
 - Mismatch detection (boarded bus but absent in school)
- **Late Arrival Handling:**
 - Bus delayed → All students on that bus excused for late arrival
 - Individual late (bus on time) → Unexcused late

**TRIGGER EVENT:**
- **Bus Boarding:** RFID scan logged
- **School Arrival:** RFID scan at school gate
- **Attendance Marking:** Cross-verification with bus logs
- **Bus Delay:** Late arrival status updated for all students on bus

**IMPACT:**
- **Attendance Validation:**
 - Aarav boards Bus Route 3 at 7:25 AM (RFID scan)
 - Bus reaches school at 8:15 AM
 - Aarav deboards (RFID scan at school gate)
 - School attendance marked at 9:00 AM: "Present"
 - System validates: Bus boarding + School attendance = Consistent
- **Mismatch Detection:**
 - Priya boards Bus Route 5 at 7:20 AM (RFID scan)
 - Bus reaches school at 8:10 AM
 - Priya deboards (RFID scan)
 - School attendance marked at 9:00 AM: "Absent" 
 - System flags: "MISMATCH: Priya boarded bus but marked absent in school"
 - Investigation triggered
 - Found: Priya went to infirmary immediately after arrival (feeling unwell)
 - Attendance corrected to "Present" (was in school, just not in class)
- **Bus Delay Handling:**
 - Bus Route 7 delayed 40 minutes due to traffic
 - All 45 students on Bus 7 arrive at 9:10 AM (school starts 8:30 AM)
 - System auto-marks all 45 students as "Present" (not "Late")
 - Reason logged: "Bus Route 7 delayed due to traffic"
 - No penalty for students

**BUSINESS LOGIC:**
```
FUNCTION reconcile_bus_and_school_attendance():
 // Run at 10 AM daily (after attendance marking)
 bus_students = GET students WHERE transport_enrolled = TRUE
 
 FOR each student IN bus_students:
 boarded_bus = CHECK boarding_log(student, TODAY)
 deboarded_at_school = CHECK deboarding_log(student, TODAY, location=school)
 school_attendance = CHECK attendance(student, TODAY)
 
 IF boarded_bus AND deboarded_at_school AND school_attendance = "ABSENT":
 // Critical mismatch
 ALERT("CRITICAL MISMATCH: {student.name} boarded bus, reached school, but marked absent")
 INVESTIGATE(student)
 NOTIFY class_teacher("Verify attendance: {student.name}")
 CALL parent("Your child reached school but marked absent, please verify")
 
 ELSE IF boarded_bus AND NOT deboarded_at_school:
 // Student boarded but didn't reach school
 ALERT("CRITICAL: {student.name} boarded bus but no school arrival record")
 CALL parent IMMEDIATELY
 NOTIFY transport_manager, principal
 TRACK bus_gps_location
 
 ELSE IF NOT boarded_bus AND school_attendance = "PRESENT":
 // Student at school but didn't use bus (alternate transport)
 LOG "Alternate transport used: {student.name}"
 INCREMENT alternate_transport_count(student)
 
 IF alternate_transport_count > 10 THIS_MONTH:
 NOTIFY transport_manager("Frequent alternate transport: {student.name}, consider withdrawal")
 END IF
 END IF
 END FOR
END FUNCTION

FUNCTION handle_bus_delay_late_arrivals(bus, delay_minutes):
 students_on_bus = GET students WHERE current_bus = bus
 
 FOR each student IN students_on_bus:
 attendance = GET attendance WHERE student = student AND date = TODAY
 
 IF attendance.status = "LATE":
 // Change from "Late" to "Present" (excused due to bus delay)
 attendance.status = "PRESENT"
 attendance.late_reason = "Bus Route {bus.route_name} delayed {delay_minutes} mins"
 attendance.excused = TRUE
 END IF
 END FOR
 
 LOG "Bus delay handled: {students_on_bus.count} students excused for late arrival"
END FUNCTION
```

**EXAMPLE:**
- **Rohan's Bus Journey with Attendance Validation:**
 - **7:15 AM:** Rohan boards Bus Route 5 (RFID scan logged)
 - **8:05 AM:** Bus reaches school, Rohan deboards (RFID scan at gate)
 - **9:00 AM:** Class teacher marks attendance, Rohan marked "Present"
 - **10:00 AM:** System reconciliation runs
 - Bus boarding: (7:15 AM)
 - School arrival: (8:05 AM)
 - School attendance: (Present)
 - Status: CONSISTENT, no issues
 - **Complete tracking:** Bus journey validated with school attendance

---

### 5. TO SECURITY & VISITOR MANAGEMENT MODULE

**WHY This Connection Exists:**
Bus arrival/departure times must sync with gate entry/exit logs. Students leaving with parents (not by bus) need gate passes. Emergency evacuations require knowing which students are on buses.

**DATA FLOW:**
- **Bus Entry/Exit Logs:**
 - Bus arrival time at school gate
 - Bus departure time from school
 - Driver identity verification
 - Vehicle inspection logs
- **Student Gate Pass Integration:**
 - Student enrolled in bus but leaving with parent → Gate pass required
 - Parent must show authorization
 - Bus driver notified (student not boarding evening bus)
- **Emergency Evacuation:**
 - Students currently on buses (morning/evening)
 - Students in school building
 - Total headcount reconciliation

**TRIGGER EVENT:**
- **Bus Arrival:** Gate entry logged
- **Bus Departure:** Gate exit logged
- **Parent Pickup Request:** Gate pass generated, bus driver notified
- **Emergency:** Headcount reconciliation

**IMPACT:**
- **Bus Entry/Exit Tracking:**
 - **Morning:**
 - 8:10 AM: Bus Route 3 arrives at school gate
 - Security guard logs: "Bus Route 3 arrived, Driver: Mr. Sharma, Vehicle: DL-1234"
 - 45 students deboard (RFID scans logged)
 - Bus parks in designated area
 - **Evening:**
 - 3:00 PM: School dismissal
 - 45 students board Bus Route 3 (RFID scans)
 - 3:15 PM: Bus departs school gate
 - Security guard logs: "Bus Route 3 departed, 45 students on board"
- **Parent Pickup with Gate Pass:**
 - Priya enrolled in Bus Route 5 (regular bus student)
 - Mother has doctor appointment, wants to pick up Priya at 1 PM
 - Mother requests gate pass via parent portal
 - Gate pass approved by class teacher
 - System notifies Bus Route 5 driver: "Priya will not board evening bus today"
 - 1:00 PM: Mother arrives at gate, shows gate pass QR code
 - Security verifies, Priya released to mother
 - Evening: Bus Route 5 driver doesn't wait for Priya (already notified)
- **Emergency Evacuation:**
 - Fire alarm at 2:30 PM (school dismissal at 3:00 PM)
 - System shows:
 - Students in building: 1,200
 - Students on buses (early departures): 15
 - Total students: 1,215
 - Evacuation: All 1,200 students exit building
 - Headcount: 1,200 accounted for 
 - 15 students on buses (already safe) 
 - Total: 1,215 

**BUSINESS LOGIC:**
```
FUNCTION log_bus_entry(bus, arrival_time):
 entry_log = CREATE GateEntryLog
 entry_log.vehicle = bus
 entry_log.vehicle_number = bus.registration_number
 entry_log.driver = bus.driver
 entry_log.arrival_time = arrival_time
 entry_log.entry_type = "BUS_ARRIVAL"
 
 // Verify driver identity
 driver_id_verified = VERIFY_DRIVER_ID(bus.driver)
 entry_log.driver_verified = driver_id_verified
 
 // Count students on bus
 students_count = COUNT students WHERE current_bus = bus
 entry_log.students_count = students_count
 
 // Vehicle inspection
 inspection = INSPECT_VEHICLE(bus)
 entry_log.inspection_status = inspection.status
 
 IF NOT driver_id_verified OR inspection.status = "FAILED":
 ALERT security_head("Bus entry issue: {bus.route_name}")
 END IF
 
 RETURN entry_log
END FUNCTION

FUNCTION handle_parent_pickup_gate_pass(student, gate_pass):
 // Verify student enrolled in transport
 IF student.transport_enrolled = TRUE:
 // Notify bus driver (student won't board evening bus)
 bus = student.transport_route.bus
 NOTIFY bus.driver("Student {student.name} will not board evening bus (parent pickup)")
 
 // Update evening roster
 UPDATE evening_bus_roster REMOVE student FROM bus.route
 END IF
 
 // Process gate pass normally
 VERIFY gate_pass
 RELEASE student TO parent
 
 LOG "Student {student.name} left with parent (gate pass), not using bus today"
END FUNCTION
```

**EXAMPLE:**
- **Aarav's Parent Pickup (Exception to Regular Bus Routine):**
 - **Regular Routine:** Aarav uses Bus Route 3 daily
 - **Today (Exception):** Father picking up Aarav at 2 PM (dentist appointment)
 - **12:00 PM:** Father requests gate pass via parent portal
 - **12:15 PM:** Class teacher approves gate pass
 - **12:16 PM:** System notifies Bus Route 3 driver: "Aarav will not board evening bus"
 - **2:00 PM:** Father arrives at school gate, shows gate pass QR code
 - **2:05 PM:** Security scans QR, verifies father's ID, releases Aarav
 - **3:15 PM:** Bus Route 3 departs without Aarav (driver already aware)
 - **No confusion:** Driver doesn't wait for Aarav, bus departs on time

---

### 6. TO MAINTENANCE & ASSET MANAGEMENT MODULE

**WHY This Connection Exists:**
Buses are school assets requiring regular maintenance. Vehicle health monitoring prevents breakdowns. Maintenance schedules ensure bus safety and reliability.

**DATA FLOW:**
- **Vehicle Information:**
 - Bus registration number
 - Make, model, year
 - Seating capacity
 - GPS device ID
 - RFID reader ID
- **Maintenance Schedule:**
 - Last service date
 - Next service due date
 - Service type (routine/major/repair)
 - Service cost
- **Vehicle Health:**
 - Odometer reading
 - Fuel consumption
 - Engine diagnostics
 - Tire condition
 - Breakdown history

**TRIGGER EVENT:**
- **Service Due:** Maintenance reminder
- **Breakdown:** Emergency repair needed
- **Inspection:** Quarterly safety inspection
- **Fuel Refill:** Fuel consumption logged

**IMPACT:**
- **Preventive Maintenance:**
 - Bus Route 3 vehicle: Last service March 1
 - Next service due: June 1 (every 3 months)
 - May 25: System reminder: "Bus Route 3 service due in 7 days"
 - May 30: Bus taken for service
 - June 1: Service completed, next due: September 1
 - Service cost: ₹15,000 (logged in asset management)
- **Breakdown Management:**
 - Bus Route 5 breaks down at 7:45 AM (engine issue)
 - Driver reports via app
 - System alerts: Transport manager, principal
 - Backup bus dispatched immediately
 - 45 students transferred to backup bus
 - Delayed arrival: 8:30 AM (45 mins late)
 - Parents notified throughout
 - Broken bus towed to workshop
 - Repair cost: ₹25,000
 - Bus back in service next day

**BUSINESS LOGIC:**
```
FUNCTION schedule_bus_maintenance(bus):
 last_service = bus.last_service_date
 service_interval = 90 days // Every 3 months
 next_service_due = last_service + service_interval
 
 bus.next_service_due = next_service_due
 
 // Send reminders
 IF next_service_due - TODAY <= 7 days:
 NOTIFY transport_manager("Bus {bus.route_name} service due in {days_remaining} days")
 END IF
 
 // Block bus from operation if overdue
 IF TODAY > next_service_due + 7 days:
 bus.operational_status = "BLOCKED"
 ALERT("CRITICAL: Bus {bus.route_name} overdue for service, blocked from operation")
 END IF
END FUNCTION

FUNCTION handle_bus_breakdown(bus, location, issue):
 breakdown = CREATE BreakdownIncident
 breakdown.bus = bus
 breakdown.location = location
 breakdown.issue = issue
 breakdown.timestamp = NOW
 
 // Get students on bus
 students_on_bus = GET students WHERE current_bus = bus
 
 // Dispatch backup bus
 backup_bus = GET available_backup_bus()
 
 IF backup_bus:
 DISPATCH backup_bus TO location
 estimated_arrival = location.distance_from_school / backup_bus.speed + 15 mins
 
 // Notify parents
 FOR each student IN students_on_bus:
 SEND SMS(parent, "Bus breakdown. Backup bus dispatched, ETA: {estimated_arrival}")
 END FOR
 ELSE:
 // No backup available, arrange alternate transport
 FOR each student IN students_on_bus:
 CALL parent("Bus breakdown, please arrange pickup at {location}")
 END FOR
 END IF
 
 // Arrange towing
 CALL tow_service
 
 // Update bus status
 bus.operational_status = "BREAKDOWN"
 
 RETURN breakdown
END FUNCTION
```

**EXAMPLE:**
- **Bus Route 7 Maintenance Cycle:**
 - **March 1:** Routine service (oil change, filter replacement, tire check)
 - **Cost:** ₹12,000
 - **Next Service Due:** June 1
 - **May 25:** System reminder to transport manager
 - **May 30:** Bus taken to workshop
 - **May 31:** Service completed
 - Oil changed 
 - Filters replaced 
 - Tires rotated 
 - Brakes checked 
 - Engine diagnostics 
 - **June 1:** Bus back in operation
 - **Next Service Due:** September 1

---

### 7. TO COMMUNICATION & NOTIFICATION MODULE

**WHY This Connection Exists:**
Transport-related communications (delays, route changes, emergencies) must reach parents instantly. Multi-channel notifications ensure critical safety messages are delivered.

**DATA FLOW:**
- **Notification Types:**
 - Bus approaching (10 mins before pickup)
 - Boarding/deboarding confirmations
 - Delay alerts
 - Route changes
 - Emergency alerts
 - Service suspension warnings
- **Communication Channels:**
 - SMS (instant, high priority)
 - Email (detailed information)
 - App push notifications (real-time)
 - Automated calls (emergencies)

**TRIGGER EVENT:**
- **Bus Approaching:** GPS-based proximity alert
- **RFID Scan:** Boarding/deboarding notification
- **Delay Detected:** Immediate alert
- **Emergency:** Multi-channel urgent alert

**IMPACT:**
- **Multi-Channel Delivery:**
 - Bus Route 3 delayed 30 minutes
 - System sends:
 - SMS to all 45 parents: "Bus delayed 30 mins, new ETA: 8:00 AM"
 - Email with detailed explanation
 - App push notification with live GPS tracking
 - Delivery tracking: 45/45 SMS delivered, 42/45 emails opened, 40/45 app notifications read
- **Emergency Communication:**
 - Bus Route 5 accident (minor, no injuries)
 - System sends:
 - SMS: "EMERGENCY: Bus accident, all students safe"
 - Email: Detailed incident report
 - App: Live updates
 - Automated call: "This is an emergency alert..."
 - All parents reached within 5 minutes

**BUSINESS LOGIC:**
```
FUNCTION send_transport_notification(notification_type, recipients, message, priority):
 FOR each recipient IN recipients:
 // SMS (always sent for high priority)
 IF priority = "HIGH" OR priority = "URGENT":
 SEND SMS(recipient.mobile, message)
 END IF
 
 // Email (detailed information)
 SEND EMAIL(recipient.email, message)
 
 // App push notification
 IF recipient.app_installed:
 SEND PUSH_NOTIFICATION(recipient.app, message)
 END IF
 
 // Automated call (only for emergencies)
 IF priority = "URGENT":
 MAKE_AUTOMATED_CALL(recipient.mobile, message)
 END IF
 
 // Track delivery
 LOG notification_delivery(recipient, notification_type, timestamp=NOW)
 END FOR
END FUNCTION
```

**EXAMPLE:**
- **Routine Notification (Bus Approaching):**
 - Priority: MEDIUM
 - Channels: SMS + App
 - Message: "Bus Route 3 approaching, ETA 8 mins"
 - Delivery: SMS sent, app notification sent
- **Emergency Notification (Accident):**
 - Priority: URGENT
 - Channels: SMS + Email + App + Automated Call
 - Message: "EMERGENCY: Bus accident, all students safe, alternate bus arranged"
 - Delivery: All channels activated, 100% reach within 5 minutes

---

### 8. TO AI & PREDICTIVE ANALYTICS MODULE

**WHY This Connection Exists:**
Transport data feeds ML models for route optimization, delay prediction, and fuel efficiency analysis. Historical data identifies patterns for better planning.

**DATA FLOW:**
- **Route Efficiency Data:**
 - Average journey time per route
 - Delay frequency and causes
 - Fuel consumption per km
 - Student boarding/deboarding times
- **Predictive Features:**
 - Traffic patterns (time of day, day of week)
 - Weather conditions
 - Road construction/closures
 - Historical delay data

**TRIGGER EVENT:**
- **Daily Journey Completion:** Data logged for analysis
- **Weekly Analytics:** Route efficiency reports
- **Delay Pattern Detected:** Route optimization recommended
- **Fuel Consumption Spike:** Maintenance alert

**IMPACT:**
- **Route Optimization:**
 - AI analyzes Bus Route 3 data:
 - Average delay: 15 mins (due to traffic at Sector 12)
 - Recommendation: Change route to avoid Sector 12
 - New route implemented, delay reduced to 5 mins
- **Delay Prediction:**
 - AI predicts: "Bus Route 5 likely to be delayed 20 mins today (heavy rain forecast)"
 - Proactive notification sent to parents before delay occurs
- **Fuel Efficiency:**
 - Bus Route 7 fuel consumption increased 20%
 - AI flags: "Possible engine issue, schedule maintenance"
 - Maintenance reveals clogged fuel filter, fixed

**BUSINESS LOGIC:**
```
FUNCTION optimize_route_using_ai(route):
 historical_data = GET route_journey_data(route, last_90_days)
 
 features = {
 average_delay: CALCULATE_AVERAGE(historical_data.delays),
 delay_frequency: COUNT(historical_data.delays > 10 mins),
 traffic_hotspots: IDENTIFY_HOTSPOTS(historical_data.gps_logs),
 fuel_consumption: CALCULATE_AVERAGE(historical_data.fuel_per_km)
 }
 
 // AI recommends route changes
 optimization = ML_MODEL.optimize_route(features)
 
 IF optimization.potential_savings > 15 mins:
 RECOMMEND route_change(route, optimization.new_route, savings=optimization.potential_savings)
 END IF
END FUNCTION
```

**EXAMPLE:**
- **AI-Optimized Route Change:**
 - **Original Route 3:** Sector 10 → 12 → 14 → 16 → School (avg 60 mins, 15 mins delay)
 - **AI Analysis:** Traffic jam at Sector 12 daily (8:00-8:30 AM)
 - **Recommended Route:** Sector 10 → 11 → 14 → 16 → School (avoid Sector 12)
 - **New Route Implemented:** Average time 50 mins, delay reduced to 5 mins
 - **Savings:** 10 mins per day × 200 school days = 2,000 mins/year saved

---

## INBOUND CONNECTIONS (Other Modules → Transport Management)

### FROM STUDENT MANAGEMENT MODULE

**WHY:** Student address changes require route reassignment.

**DATA RECEIVED:**
- Address changes (family relocation)
- New admissions (transport enrollment)
- Withdrawals (remove from bus roster)

**IMPACT:**
- Route reassignment when address changes
- New students added to appropriate routes
- Withdrawn students removed, refunds processed

**TRIGGER:** Address change, admission, withdrawal

---

### FROM FEE MANAGEMENT MODULE

**WHY:** Payment status determines transport access.

**DATA RECEIVED:**
- Transport fee payment status
- Outstanding dues
- Payment confirmations

**IMPACT:**
- Service suspended if payment overdue 60 days
- Access restored after payment
- RFID card blocked/unblocked

**TRIGGER:** Payment made/overdue

---

### FROM ATTENDANCE MANAGEMENT MODULE

**WHY:** School attendance validates bus usage.

**DATA RECEIVED:**
- Daily school attendance
- Leave requests
- Absence records

**IMPACT:**
- Bus attendance reconciled with school attendance
- Mismatches investigated
- Alternate transport usage tracked

**TRIGGER:** Attendance marked, mismatch detected

---

### FROM PARENT PORTAL

**WHY:** Parents request transport enrollment/withdrawal.

**DATA RECEIVED:**
- Transport enrollment requests
- Withdrawal requests
- Route change requests

**IMPACT:**
- Enrollment processed, route assigned
- Withdrawal processed, refund calculated
- Route changes accommodated if possible

**TRIGGER:** Parent request submitted

---

### FROM SECURITY MODULE

**WHY:** Gate entry/exit logs validate bus movements.

**DATA RECEIVED:**
- Bus entry/exit times
- Driver verification logs
- Vehicle inspection results

**IMPACT:**
- Bus movements tracked
- Driver identity verified
- Safety compliance ensured

**TRIGGER:** Bus entry/exit at gate

---

### FROM WEATHER & TRAFFIC SERVICES (EXTERNAL API)

**WHY:** Weather and traffic affect bus schedules.

**DATA RECEIVED:**
- Weather forecasts (rain, fog)
- Traffic alerts (jams, accidents)
- Road closures

**IMPACT:**
- Delay predictions
- Route adjustments
- Proactive parent notifications

**TRIGGER:** Weather/traffic alerts

---

### FROM MAINTENANCE MODULE

**WHY:** Vehicle maintenance affects bus availability.

**DATA RECEIVED:**
- Service schedules
- Breakdown reports
- Vehicle health status

**IMPACT:**
- Buses taken out of service for maintenance
- Backup buses deployed
- Route adjustments if bus unavailable

**TRIGGER:** Service due, breakdown

---

### FROM EMERGENCY SERVICES (EXTERNAL)

**WHY:** Accidents and emergencies require immediate response.

**DATA RECEIVED:**
- Accident reports
- Ambulance dispatch confirmations
- Police reports

**IMPACT:**
- Emergency protocols activated
- Parents notified immediately
- Incident reports generated

**TRIGGER:** Emergency reported

---

## SUMMARY

**Transport Management Module connects to 15+ modules**

**Critical Outbound Dependencies:**
1. Student Management (route assignment, safety tracking)
2. Parent Portal (GPS tracking, notifications)
3. Fee Management (transport fees, service suspension)
4. Attendance (bus attendance validation)
5. Security (gate entry/exit, student release)

**Critical Inbound Dependencies:**
1. Student Management (address changes, enrollments)
2. Fee Management (payment status)
3. Attendance (school attendance validation)
4. Weather/Traffic APIs (delay predictions)
5. Maintenance (vehicle availability)

**Transport Metrics:**
- **Fleet Size:** 25 buses (example school)
- **Routes:** 15 routes covering 50 km radius
- **Students Enrolled:** 800/1,500 students (53%)
- **Daily Trips:** 50 trips (25 buses × 2 trips/day)
- **Average Route Time:** 45-60 minutes
- **On-Time Performance:** 92% (target: 95%)
- **Safety Incidents:** 0 (target: 0)

**Safety Features:**
- RFID-based boarding/deboarding tracking
- Live GPS tracking for parents
- Multi-channel emergency alerts
- Driver identity verification
- Vehicle safety inspections
- Backup bus availability

**Data Freshness:**
- Real-time: GPS location, RFID scans, boarding/deboarding
- Every 30 seconds: GPS updates during journey
- Hourly: Route efficiency analysis
- Daily: Journey completion logs, fuel consumption
- Weekly: Route optimization recommendations
- Monthly: Maintenance schedules, cost analysis

This module is the **"student safety & logistics backbone"** - ensuring safe, reliable, and efficient transportation for hundreds of students daily while keeping parents informed and reassured.


---

# Submodule Breakdown

# TRANSPORT MANAGEMENT MODULE - SUBMODULE OVERVIEW

**Module Code:** TRANS-011  
**Category:** Operations  
**Priority:** P1  
**Owner:** Transport Team

## Submodule Breakdown

This module is divided into **7 submodules**, each handling a specific aspect of transport management management:

### 1. Route Planning & Optimization
**Code:** TRANS-001  
**File:** `01_route_planning_optimization.md`  
**Purpose:** Route Planning & Optimization functionality  

### 2. Bus & Driver Assignment
**Code:** TRANS-002  
**File:** `02_bus_driver_assignment.md`  
**Purpose:** Bus & Driver Assignment functionality  

### 3. Student Enrollment & Allocation
**Code:** TRANS-003  
**File:** `03_student_enrollment_allocation.md`  
**Purpose:** Student Enrollment & Allocation functionality  

### 4. RFID/GPS Tracking
**Code:** TRANS-004  
**File:** `04_rfid_gps_tracking.md`  
**Purpose:** RFID/GPS Tracking functionality  

### 5. Parent Notifications (Boarding/Deboarding)
**Code:** TRANS-005  
**File:** `05_parent_notifications_boarding_deboarding.md`  
**Purpose:** Parent Notifications (Boarding/Deboarding) functionality  

### 6. Transport Fee Management
**Code:** TRANS-006  
**File:** `06_transport_fee_management.md`  
**Purpose:** Transport Fee Management functionality  

### 7. Vehicle Maintenance Tracking
**Code:** TRANS-007  
**File:** `07_vehicle_maintenance_tracking.md`  
**Purpose:** Vehicle Maintenance Tracking functionality  

## Integration Points

Transport Management connects to multiple modules across the Hogwarts ERP system.

## Development Priority

**Phase 1 (Critical):** Submodules 1-3  
**Phase 2 (High):** Submodules 4-5  
**Phase 3 (Medium):** Remaining submodules  

---

**Status:** Submodule structure defined ⏳  
**Last Updated:** January 15, 2026  
**Note:** Detailed submodule documentation to be created
