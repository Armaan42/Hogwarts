# CANTEEN & NUTRITION MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Canteen & Nutrition Module  
**Role:** School Canteen Operations, Meal Management & Nutritional Tracking  
**Type:** Critical Operations & Student Welfare Module  
**Dependencies:** Integrates with Student Management, Fee Management, Health modules  

**Primary Functions:**
- Menu Planning - Weekly/monthly menus, balanced nutrition
- Meal Ordering - Pre-order system, daily meal selection
- Nutrition Tracking - Calorie count, dietary requirements
- Canteen Wallet - Prepaid digital wallet for students
- Vendor Management - Food supplier contracts, quality control
- Food Safety - FSSAI compliance, hygiene standards
- Dietary Accommodations - Vegetarian, vegan, allergies, religious
- Meal Analytics - Consumption patterns, waste reduction
- Feedback System - Student/parent ratings, menu improvements
- Inventory Management - Stock tracking, expiry management

---

## OUTBOUND CONNECTIONS (Canteen → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY:** Track student dietary preferences, allergies, meal consumption history.

**DATA FLOW:**
- Student dietary preferences (vegetarian, vegan, Jain, etc.)
- Allergy information (nuts, dairy, gluten)
- Meal consumption history
- Canteen wallet balance

**TRIGGER:** Student enrollment, preference update, meal purchase

**IMPACT:** Personalized meal recommendations, allergy alerts

---

### 2. TO FEE MANAGEMENT MODULE

**WHY:** Canteen wallet recharge, meal plan payments.

**DATA FLOW:**
- Wallet recharge transactions
- Meal plan subscriptions (monthly, quarterly)
- Payment history

**TRIGGER:** Parent recharges wallet, subscribes to meal plan

**IMPACT:** ₹500 wallet recharge, ₹3,000/month meal plan

---

## INBOUND CONNECTIONS (Other Modules → Canteen)

### FROM HEALTH MODULE

**WHY:** Medical dietary restrictions, nutritionist recommendations.

**DATA RECEIVED:**
- Medical conditions (diabetes, obesity, anemia)
- Nutritionist diet plans
- BMI data for portion control

**IMPACT:** Diabetic student gets low-sugar meals, obese student gets calorie-controlled portions

---

## CANTEEN OPERATIONS

### Daily Operations Schedule

**6:00 AM - Kitchen Preparation**
- Staff arrives, hygiene checks
- Ingredients received, quality inspection
- Cooking begins (breakfast items)

**7:30 AM - Breakfast Service**
- Breakfast counter opens
- Items: Idli, dosa, poha, upma, sandwiches
- Service until 9:00 AM

**10:30 AM - Mid-Morning Snacks**
- Snack counter opens
- Items: Fruits, juice, cookies, samosas
- Service until 11:30 AM

**12:00 PM - Lunch Preparation**
- Main meal cooking (rice, roti, dal, sabzi)
- Salad preparation
- Dessert preparation

**12:30 PM - Lunch Service (Primary)**
- Grades 1-5 lunch (600 students)
- Service until 1:15 PM

**1:15 PM - Lunch Service (Secondary)**
- Grades 6-12 lunch (1,200 students)
- Service until 2:30 PM

**3:30 PM - Evening Snacks**
- Snack counter reopens
- Items: Chai, pakoras, sandwiches
- Service until 5:00 PM

**5:30 PM - Kitchen Cleanup**
- Cleaning, sanitization
- Waste disposal
- Stock inventory update

---

## MENU PLANNING

### Weekly Menu (Sample - Week 1)

**Monday:**
- Breakfast: Idli + Sambar + Chutney
- Lunch: Jeera Rice + Dal Tadka + Mix Veg + Roti + Salad + Curd
- Snacks: Samosa + Chai

**Tuesday:**
- Breakfast: Poha + Jalebi
- Lunch: Veg Pulao + Raita + Papad + Salad + Gulab Jamun
- Snacks: Bread Pakora + Juice

**Wednesday:**
- Breakfast: Upma + Banana
- Lunch: Roti + Rajma + Rice + Aloo Gobi + Salad + Pickle
- Snacks: Vada Pav + Chai

**Thursday:**
- Breakfast: Dosa + Chutney + Sambar
- Lunch: Chole Bhature + Salad + Lassi
- Snacks: Pani Puri + Juice

**Friday:**
- Breakfast: Paratha + Curd + Pickle
- Lunch: Veg Biryani + Raita + Salad + Ice Cream
- Snacks: Pizza Slice + Cold Drink

**Menu Rotation:** 4-week cycle (prevents boredom)

---

## NUTRITION STANDARDS

### Daily Nutritional Requirements (Per Student)

**Age 6-10 years:**
- Calories: 1,600-1,800 kcal
- Protein: 25-30g
- Carbs: 200-250g
- Fat: 40-50g
- Fiber: 20-25g

**Age 11-15 years:**
- Calories: 2,000-2,400 kcal
- Protein: 40-50g
- Carbs: 250-300g
- Fat: 50-60g
- Fiber: 25-30g

**Age 16-18 years:**
- Calories: 2,400-2,800 kcal
- Protein: 50-60g
- Carbs: 300-350g
- Fat: 60-70g
- Fiber: 30-35g

### Meal Composition Guidelines

**Lunch (Main Meal):**
- 40% Carbohydrates (rice, roti)
- 30% Protein (dal, paneer, soya)
- 20% Vegetables (sabzi, salad)
- 10% Dairy/Dessert (curd, sweet)

**Balanced Plate Example:**
```
╔════════════════════════════════════════╗
║        BALANCED LUNCH PLATE            ║
╠════════════════════════════════════════╣
║                                        ║
║  🍚 Rice (1 cup) - 200 kcal           ║
║  🫓 Roti (2 pcs) - 150 kcal           ║
║  🍛 Dal (1 bowl) - 120 kcal           ║
║  🥗 Mix Veg (1 bowl) - 80 kcal        ║
║  🥗 Salad (1 plate) - 30 kcal         ║
║  🥛 Curd (1 bowl) - 60 kcal           ║
║  🍨 Sweet (small) - 100 kcal          ║
║                                        ║
║  Total: 740 kcal                       ║
║  Protein: 22g | Carbs: 110g | Fat: 18g║
╚════════════════════════════════════════╝
```

---

## FOOD SAFETY & HYGIENE

### FSSAI Compliance

**License:** FSSAI Registration No. 12345678901234  
**Validity:** Valid until March 2027  
**Inspections:** Quarterly (4 times/year)  
**Last Inspection:** December 2024 (Rating: 4.5/5.0)

**Compliance Checklist:**
- [ ] Food handlers have health certificates
- [ ] Kitchen cleaned daily (deep clean weekly)
- [ ] Pest control done monthly
- [ ] Water quality tested quarterly
- [ ] Temperature logs maintained
- [ ] Expiry dates checked daily
- [ ] Waste segregation followed

### Hygiene Protocols

**Staff Hygiene:**
- Hairnets, gloves, aprons mandatory
- Hand washing every 30 minutes
- No jewelry, nail polish
- Annual health checkups

**Kitchen Hygiene:**
- Stainless steel surfaces (easy to clean)
- Separate cutting boards (veg/non-veg)
- Daily sanitization
- Pest control monthly

**Food Storage:**
- Refrigeration: 0-4°C (perishables)
- Dry storage: Cool, dry place
- FIFO method (First In, First Out)
- Expiry tracking system

---

## CANTEEN WALLET SYSTEM

### Digital Wallet Features

**How It Works:**
1. Parent recharges wallet (₹500, ₹1,000, ₹2,000)
2. Student uses RFID card to pay
3. Balance deducted automatically
4. Parent gets SMS notification

**Benefits:**
- Cashless transactions (safe, hygienic)
- Spending limit control (₹100/day max)
- Transaction history (parent portal)
- Auto-recharge option (when balance < ₹100)

**Wallet Statistics (2024-25):**
- Active wallets: 1,500 (83% students)
- Average balance: ₹350
- Monthly recharges: ₹15L
- Transaction volume: 25,000/month

**Popular Items (by wallet spend):**
1. Lunch meals: ₹8L/month (53%)
2. Snacks: ₹4L/month (27%)
3. Beverages: ₹2L/month (13%)
4. Desserts: ₹1L/month (7%)

---

## DIETARY ACCOMMODATIONS

### Special Diets Supported

**1. Vegetarian (100% students):**
- No meat, fish, eggs
- All menu items vegetarian

**2. Vegan (50 students, 3%):**
- No dairy, honey
- Soy milk, coconut curd alternatives

**3. Jain (30 students, 2%):**
- No onion, garlic, root vegetables
- Special Jain counter

**4. Gluten-Free (20 students, 1%):**
- Celiac disease, gluten intolerance
- Rice-based items, gluten-free roti

**5. Nut Allergy (15 students, 0.8%):**
- No peanuts, tree nuts
- Strict segregation, labeling

**6. Diabetic (10 students, 0.6%):**
- Low sugar, low GI foods
- Sugar-free desserts

**Accommodation Process:**
```
1. Parent submits dietary requirement form
2. Medical certificate (if medical condition)
3. Canteen manager reviews
4. Special meal plan created
5. Kitchen staff trained
6. Student RFID card flagged (allergy alert)
7. Separate serving counter (if needed)
```

---

## MEAL ANALYTICS

### Consumption Patterns

**Most Popular Items:**
1. Chole Bhature (Friday) - 95% students
2. Veg Biryani (Friday) - 92% students
3. Pizza (Friday snacks) - 88% students
4. Samosa (Monday snacks) - 85% students
5. Ice Cream (Friday dessert) - 90% students

**Least Popular Items:**
1. Bitter Gourd Sabzi - 30% students
2. Lauki (Bottle Gourd) - 35% students
3. Spinach Dal - 40% students

**Waste Reduction Strategy:**
- Unpopular items removed from menu
- Portion sizes optimized (reduce waste)
- "Take what you eat" campaign
- Composting food waste (400 tons/year)

**Waste Statistics:**
- Food waste: 50 kg/day (down from 200 kg in 2023)
- Waste reduction: 75% (via portion control, composting)
- Compost produced: 15 tons/year (used in school garden)

---

## VENDOR MANAGEMENT

### Food Suppliers

**Primary Vendors:**

**1. Fresh Vegetables:**
- Vendor: Green Farm Suppliers
- Contract: ₹5L/year
- Delivery: Daily (6 AM)
- Quality: Organic (50%), conventional (50%)

**2. Dairy Products:**
- Vendor: Amul Dairy
- Contract: ₹3L/year
- Delivery: Daily (6 AM)
- Products: Milk, curd, paneer, butter

**3. Grains & Pulses:**
- Vendor: Grain Traders Co.
- Contract: ₹4L/year
- Delivery: Weekly
- Quality: Premium grade

**4. Snacks & Packaged:**
- Vendor: Haldiram's
- Contract: ₹2L/year
- Delivery: Bi-weekly
- Products: Namkeen, sweets, biscuits

**Vendor Selection Criteria:**
- FSSAI license (mandatory)
- Quality certifications (ISO, organic)
- Competitive pricing
- Timely delivery (99%+ on-time)
- Hygiene standards

**Quality Control:**
- Daily inspection of delivered items
- Random sampling for lab testing
- Vendor performance scorecard (monthly)
- Annual vendor review

---

## STUDENT FEEDBACK SYSTEM

### Monthly Feedback Survey

**Questions:**
1. How would you rate today's meal? (1-5 stars)
2. Which item did you like most?
3. Which item did you dislike?
4. Any suggestions for menu?

**Results (December 2024):**
- Response rate: 70% (1,260/1,800 students)
- Average rating: 4.2/5.0
- Most liked: Veg Biryani (Friday)
- Most disliked: Bitter Gourd Sabzi
- Top suggestion: "More pizza, less vegetables"

**Action Taken:**
- Bitter Gourd removed from menu
- Pizza frequency increased (1x/week → 2x/week)
- New item added: Pasta (student request)

---

## FINANCIAL OVERVIEW

**Annual Canteen Budget:** ₹72 Lakh

**Revenue:**
- Meal sales: ₹60L (83%)
- Snack sales: ₹10L (14%)
- Beverage sales: ₹2L (3%)

**Expenses:**
- Raw materials: ₹40L (56%)
- Staff salaries: ₹15L (21%)
- Utilities (gas, electricity): ₹8L (11%)
- Equipment maintenance: ₹3L (4%)
- Licenses, inspections: ₹2L (3%)
- Miscellaneous: ₹4L (5%)

**Profit:** ₹0 (non-profit, break-even model)

**Subsidy:** School subsidizes ₹10L/year (to keep prices affordable)

**Meal Pricing:**
- Breakfast: ₹30-50
- Lunch: ₹60-80
- Snacks: ₹20-40
- Beverages: ₹10-30

---

## TECHNOLOGY INTEGRATION

**Canteen Management Software:**
- Menu planning module
- Inventory tracking
- RFID wallet system
- Analytics dashboard
- Feedback collection

**Mobile App (Parents):**
- View menu (weekly)
- Recharge wallet
- Set spending limits
- View transaction history
- Submit dietary requirements

**RFID System:**
- Student card (contactless payment)
- Transaction time: 2 seconds
- Balance display on screen
- Low balance alert

---

## BEST PRACTICES

**Top 10 Canteen Best Practices:**

1. **Balanced Nutrition:** Follow FSSAI guidelines
2. **Hygiene First:** Daily cleaning, monthly pest control
3. **Fresh Ingredients:** Daily procurement of vegetables
4. **Variety:** 4-week menu rotation
5. **Student Feedback:** Monthly surveys, menu adjustments
6. **Waste Reduction:** Portion control, composting
7. **Dietary Accommodations:** Support all dietary needs
8. **Cashless System:** RFID wallets (safe, convenient)
9. **Quality Control:** Daily inspections, lab testing
10. **Transparency:** Menu displayed, nutritional info available

---

**Status:** Production-Ready  
**Last Updated:** January 16, 2026  
**Version:** 1.0
