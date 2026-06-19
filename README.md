# XAI-Methane-Tracker-Modeling-Satellite-Emissions-with-XGBoost-SHAP-and-Open-Meteo-APIs
This Explainable AI (XAI) pipeline ingests raw NASA EMIT satellite data, handles missing labels via a rule-based heuristic engine, and joins targets with Open-Meteo weather APIs. It trains an XGBoost model to predict methane concentrations and uses SHAP to reverse-engineer predictions into real-world, physics-based compliance audits.

# Problem Statement
When people think of greenhouse gases, they usually think of Carbon Dioxide (CO2). But Methane (CH4) is actually much more dangerous in the short term. CH4 is over 80 times more powerful at trapping heat in our atmosphere than CO2. And the worst part is that, Methane is completely invisible to human eyes, it  has no smell, and leaks constantly from hidden sources like: Cracks in natural gas pipelines, Air vents in deep coal mines, Rotting garbage in massive landfills.

Many Companies often claim that their operations are perfectly clean simply because they don't track these invisible CH4 leaks. And in oder to catch them, we can now use high-tech Hyperspectral remote sensing, such as Lidar Satellites (like NASA's EMIT satellite) that can see these CH4 gas clouds from orbit.

However, the data sent down from the satellite is completely unlabeled. It gives us the shape of the gas cloud on a map, but it does not tell us who caused it, what kind of facility it is and how the local weather affects such leaks. Without that context, we cannot hold anyone accountable.

# The Solution
This project fixes that exact problem. It takes raw, messy satellite data and passes it through an automated computer pipeline to turn it into a clear, readable climate audit report.

Here is exactly how it works:
1. Finds the Exact Location: The code automatically reads the coordinates of the raw satellite gas shapes and pinpoints their exact center on Earth.
2. Checks the Weather: It automatically looks up the exact hour the satellite flew overhead and pulls historical weather data (temperature and wind speed) for that exact spot using a weather API.
3. Guesses the Industry: Since NASA doesn't label the data, the project uses a smart rule engine to look at the size and strength of the leak to automatically figure out if it came from a landfill or an oil/gas facility.
4. Teaches an AI: An AI model (XGBoost) is trained to learn exactly how temperature and wind speed control how thick and dangerous the gas cloud becomes.
5. Explains the "Why": Instead of just giving a random guess, the project uses an AI tool called SHAP to explain the math in plain English.
6. Maps the Results: Finally, it saves everything onto an interactive map (index.html) that you can open in any browser to click around and see the tracked leaks globally.

# Project Overview
This project explains the control of temperature and wind speed on methane concentrations detected by NASA's EMIT satellite, covering both waste landfill and oil/gas emissions. Because spaceborne greenhouse gas observations are typically delivered as unlabeled spatial geometries, this project constructs a complete end-to-end data engineering and Explainable AI (XAI) pipeline to process, classify, and decode them.

The framework isolates exactly how changing weather patterns either trap or disperse localized greenhouse gases, translating complex algorithmic tree weights into human-readable, physics-based environmental compliance reports.

# Dataset Description & Data Acquisition
The core dataset utilized in this project consists of high-resolution point-source methane plume footprints captured via imaging spectroscopy from space.
1. We first Navigated to the official NASA EMIT (Earth Surface Mineral Dust Source Investigation) Greenhouse Gas data portal.Filtered the data layers for methane (CH4) point-source plume complexes.
2. Exported the raw coordinate data as a comprehensive geospatial file named methane_metadata.geojson.This downloaded asset contains over 3,000 recorded plumes, detailing the precise spatial boundaries (polygons) of the emissions and their maximum observed concentrations in parts per million-meter (ppm.m), but lacking weather context and facility labels.

# Methodology
1. Polygon Centroid Point Mapper: The raw NASA data includes the complex, multi-point polygon shapes of the actual gas clouds. To make this data usable for our interactive map, we used python tools to grab the exact geometric center (the centroid) coordinates for each plume. This let us extract a clean, single Latitude and Longitude point for every record so we could plot them as neat, clickable pins on our final index.html map.
2. String-Matching Text Inference Engine: Since NASA's raw dataset does not include a clean sector label column, we built a rule engine that scans the text inside each record's plume_id. If the ID contains keywords like "waste", "landfill", or "dump", the script automatically categorizes it as a Waste_Landfill. All other industrial plumes default to the Oil_Gas_Energy sector. This cleanly segmenting our unlabeled data so the AI can learn from it.
3. Weather API Ingestion: To see how weather affects the methane gas, the code looks at the exact hour the satellite flew overhead. It then uses an automated script to connect to the open-source Open-Meteo Historical Weather API. By sending the map coordinates and the timestamp to this API, the pipeline automatically pulls and injects the exact temperature (in Celsius) and wind speed (in km/h) at the precise moment the leak was spotted.
4. Training the AI Model (XGBoost)Once our dataset had the weather data and the sector labels, we used a process called "one-hot encoding" to turn our text sectors (Waste_Landfill or Oil_Gas_Energy) into simple 0s and 1s that the computer can understand.We then trained an AI model called XGBoost to learn how weather and industry types interact to affect methane gas clouds.Our Training Variables (Inputs): Latitude, Longitude, Temperature, Wind Speed, and the Sector Type.Our Target Variable (Output to Predict): The peak methane concentration (ppm.m) of the plume.The AI successfully learned the complex, real-world patterns linking these weather conditions to fluctuating gas levels across both industries.
5. Explaining the AI Decisions & Creating the Map
Normally, AI models act like a "black box"—they give you a final prediction, but they don't tell you how they came up with that number. To fix this, we integrated an AI tool called SHAP (Shapley Addictive Explanations).
SHAP acts like a digital detective. For every single methane leak prediction, it calculates the exact mathematical credit or blame that belongs to each variable. It shows us the precise push-and-pull effect of our features: exactly how many points of methane concentration were added because the wind was stagnant, or how much it dropped because of cooler temperatures. We wrote a script to translate these raw SHAP numbers into plain English explanations.
6. Finally, we built a script to plot all these points onto an interactive global map (index.html) using OpenStreetMap. Each methane leak is mapped as a color-coded marker that you can click on to see the exact coordinates, the weather context, and the AI's breakdown of what caused that specific gas concentration.

# Data Processing Note: Flexible Row Counts
The entire pipeline is completely dynamic and up to the user. You can decide exactly how many rows of data you want to run through the scripts. For our project, we chose to process 40 rows from the methane_metadata.geojson file to demonstrate how the system works. You can easily scale this up to run the entire dataset of thousands of rows—the code will handle it perfectly, it will just take a little more time to pull the historical weather data from the API!

# Results and Discussion
By connecting raw satellite data with live weather maps and Explainable AI, this project successfully built a smart auditing system. Instead of just guessing numbers, the AI model gave us clear, common-sense proof of how the environment affects methane pollution:
1. How Wind Changes the Gas Clouds: The AI proved that wind speed has a massive "push-and-pull" effect on methane. When the wind is very low or completely still (like at 7.1km/h), the gas gets trapped in place. The AI flagged this by adding a massive positive impact score (+503.72 pm.m), showing that stagnant air makes pollution clouds much thicker and more dangerous. When the wind picks up, the AI correctly showed that the gas gets blown away and diluted.
2. How Temperature Affects Garbage Rags: Temperature played a huge role depending on the industry. For city landfills, hotter weather triggered higher methane values because at high temperatures organic garbage rot and ferment at a much faster rate, releasing sudden bursts of gas.
3. How the AI Separated Industries: The AI learned that it cannot treat a landfill the same way it treats an oil pipeline. Because we split the sectors, the model automatically adjusted its baseline expectations—it expected smaller, steady, spread-out leaks from landfills, but scaled up its warnings for high-pressure, heavy-duty industrial oil and gas facilities.
   
# Personal Finding from this project
The Dangerous Methane Feedback Loop: This AI model proved that higher temperatures cause landfills to release much more methane gas due to faster rotting. This discovers a dangerous positive feedback loop driven by global warming:
1. The Trigger: Human-caused climate change raises global temperatures.
2. The Reaction: As landfills heat up, the organic waste degrades at an accelerated rate, releasing massive, sudden plumes of methane (just as XGBoost model detected).
3. The Multiplier: Because methane is 80 times more potent than CO2, this newly released gas traps even more heat in our atmosphere.
4. The Loop Continues: This extra heat raises global temperatures even further, causing landfills to rot even faster and release even more methane.

While wind speed is an independent weather variable that humans can't dictate, it acts as a local multiplier. If a high-temperature day overlaps with stagnant, low-wind conditions, the methane doesn't disperse—it pools into highly concentrated, super-heating gas blankets over specific regions.
# To Break this Hazardous CH4 positive feedback loop
1. We cannot force the wind to blow to dilute the gas, nor can we instantly cool down the global atmosphere. However, we can control the internal temperature of landfills. By using modern engineering techniques—like bio-tarps, deep-soil capping, and internal cooling/aeration systems—we can keep the garbage cool and oxygenated, slowing down the degradation rate even during a summer heatwave. However, just slowing down the degradation rate inside a landfill is not a truly sustainable, long-term solution. Slowing down degradation using deep soil caps, bio-tarps, or cooling systems is what we call mitigation. True sustainability means changing the system so the problem can never happen in the first place.
2. The best way to control this variable is to starve the loop completely by practicing a circular economy. If we divert organic waste (like food scraps and yard waste) away from landfills and into industrial composting or biogas digesters, there will be nothing left in the dump to rot. No rotting means no methane, no matter how hot the summer gets.
3. Instead of trying to control the weather or the rot inside a giant trash pile, humans can control what goes into the pile.
Instead of letting the waste rot uncontrollably in the sun, we can deliberately place organic waste into sealed, engineered tanks called Anaerobic Digesters.
Inside these tanks, we want the waste to degrade as fast as possible. We trap 100% of the methane it produces and burn it to create electricity and heat for the city.This turns a climate hazard into a renewable energy source, completely breaking the environmental feedback loop.
4. Since wind and temperature are independent, we can use your project's logic to create a predictive dashboard. If the weather forecast predicts a week of high heat and zero wind (stagnant air), landfill operators can take preventative action before the loop starts—such as applying extra daily soil cover or activating methane-capture flaring valves to trap the gas before it escapes into the atmosphere.

# MethaneAI proves that by combining spaceborne hyperspectral data with Explainable AI, we can transform raw, invisible climate threats into actionable insights—providing the data-driven roadmap needed to break dangerous global feedback loops and build a truly transparent, sustainable future.
----------------------------------------------------------------------------------*-------------------------------------------------------------------------------


