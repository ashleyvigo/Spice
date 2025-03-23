<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spice Bot: AI Meal Prep Tool</title>
    <script src="https://unpkg.com/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" integrity="sha512-9usAa10IRO0HhonpyAIVpjrylPvoDwiPUiKdWk5t3PyolY1cOd4DSE0Ga+ri4AuTroPR5aQvXU9xC6qOPnzFeg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .input-group label {
            transition: transform 0.2s ease-in-out, color 0.2s ease-in-out;
        }
        .input-group input:focus + label,
        .input-group input:valid + label {
            transform: translateY(-1rem) scale(0.8);
            color: #6b7280;
        }
        .form-control {
            display: block;
            width: 100%;
            padding: 0.75rem 1rem;
            font-size: 1rem;
            line-height: 1.5rem;
            border-radius: 0.5rem;
            border: 1px solid #d1d5db;
            appearance: none;
            transition: border-color 0.15s ease-in-out, shadow-sm 0.15s ease-in-out;
        }
        .form-control:focus {
            outline: none;
            border-color: #3b82f6;
            box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
        }
        .input-group {
            position: relative;
            margin-bottom: 1.5rem;
        }
        .input-group label {
            position: absolute;
            left: 1rem;
            top: 0.75rem;
            color: #6b7280;
            font-size: 1rem;
            pointer-events: none;
            transition: transform 0.2s ease-in-out, color 0.2s ease-in-out;
            background-color: white; /* Add this line */
            padding: 0 0.25rem; /* Add this line */
        }
        .input-group input:focus + label,
        .input-group input:valid + label {
            transform: translateY(-1rem) scale(0.8);
            color: #3b82f6;
        }
        /* Chart Styles */
        #progress-chart-container {
            width: 100%;
            margin: 2rem auto;
            background: white;
            border-radius: 0.5rem;
            padding: 1rem;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        #progress-chart-canvas {
            width: 100%;
            height: 300px; /* Adjust as needed */
        }
        #pot-pilot-title {
            font-family: 'Arial Black', sans-serif;  /* Use a bold, modern font */
            font-size: 2.5rem;          /* Make it large */
            color: #4F46E5;             /* A vibrant color */
            text-shadow: 2px 2px 4px rgba(0,0,0,0.2);  /* Add a subtle shadow */
            letter-spacing: -0.02em;    /* Slightly reduce letter spacing for a tighter look */
            transition: transform 0.2s ease, color 0.2s ease; /* Smooth transition */
            cursor: pointer;           /* Indicate interactivity */
        }

        #pot-pilot-title:hover {
            transform: scale(1.05);  /* Slightly enlarge on hover */
            color: #6366F1;       /* Change color on hover */
        }
    </style>
</head>
<body class="bg-gray-100 p-8">
    <div class="max-w-4xl mx-auto bg-white rounded-lg shadow-md p-8">
        <h1 id="pot-pilot-title" class="text-3xl font-semibold text-blue-600 text-center mb-6">Spice Bot</h1>
        <div id="input-section" class="mb-8 rounded-lg shadow-sm">
            <div class="bg-gradient-to-r from-blue-100 to-purple-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa-utensils mr-2 text-blue-600"></i> 1. Input Your Information
                </h2>
            </div>
            <div class="p-6">
                <div class="input-group">
                    <input type="text" id="dietary-restrictions" required class="form-control">
                    <label for="dietary-restrictions" class="text-gray-700 text-sm font-bold">Dietary Restrictions:</label>
                </div>
                <div class="input-group">
                    <input type="text" id="preferences" required class="form-control">
                    <label for="preferences" class="text-gray-700 text-sm font-bold">Preferences:</label>
                </div>
                <div class="input-group">
                    <input type="text" id="nutritional-goals" required class="form-control">
                    <label for="nutritional-goals" class="text-gray-700 text-sm font-bold">Nutritional Goals:</label>
                </div>
                 <div class="input-group">
                    <input type="text" id="schedule" required class="form-control">
                    <label for="schedule" class="text-gray-700 text-sm font-bold">Schedule:</label>
                </div>
                <div class="input-group">
                    <input type="text" id="budget" required class="form-control">
                    <label for="budget" class="text-gray-700 text-sm font-bold">Budget:</label>
                </div>
                <div class="mb-4">
                    <button id="set-goals-button" class="bg-gradient-to-r from-purple-400 to-pink-500 hover:from-purple-500 hover:to-pink-600 text-white font-bold py-3 px-6 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                        <i class="fas fa-bullseye mr-2"></i> Set Goals
                    </button>
                </div>
                <button id="generate-button" class="bg-gradient-to-r from-green-400 to-blue-500 hover:from-green-500 hover:to-blue-600 text-white font-bold py-3 px-6 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                    <i class="fas fa-magic mr-2"></i> Generate Meal Plan
                </button>
            </div>
        </div>
        <div id="goals-section" class="hidden mb-8 rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
            <div class="bg-gradient-to-r from-purple-100 to-pink-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa-bullseye mr-2 text-purple-600"></i> 2. Set Your Goals
                </h2>
            </div>
            <div class="p-6">
                <div class="input-group">
                    <input type="number" id="calorie-target" required class="form-control" placeholder="e.g., 2000">
                    <label for="calorie-target" class="text-gray-700 text-sm font-bold">Calorie Target (calories):</label>
                </div>
                <div class="input-group">
                    <input type="number" id="protein-ratio" required class="form-control" placeholder="e.g., 30">
                    <label for="protein-ratio" class="text-gray-700 text-sm font-bold">Protein Ratio (%):</label>
                </div>
                <div class="input-group">
                    <input type="number" id="carb-ratio" required class="form-control" placeholder="e.g., 40">
                    <label for="carb-ratio" class="text-gray-700 text-sm font-bold">Carb Ratio (%):</label>
                </div>
                <div class="input-group">
                    <input type="number" id="fat-ratio" required class="form-control" placeholder="e.g., 30">
                    <label for="fat-ratio" class="text-gray-700 text-sm font-bold">Fat Ratio (%):</label>
                </div>
                <div class="input-group">
                    <input type="number" id="fiber-goal" required class="form-control" placeholder="e.g., 25">
                    <label for="fiber-goal" class="text-gray-700 text-sm font-bold">Fiber Goal (grams):</label>
                </div>
                <div class="input-group">
                    <input type="number" id="sugar-limit" required class="form-control" placeholder="e.g., 50">
                     <label for="sugar-limit" class="text-gray-700 text-sm font-bold">Sugar Limit (grams):</label>
                </div>
                <button id="save-goals-button" class="bg-gradient-to-r from-blue-400 to-green-500 hover:from-blue-500 hover:to-green-600 text-white font-bold py-3 px-6 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                    <i class="fas fa-save mr-2"></i> Save Goals
                </button>
            </div>
        </div>
        <div id="output-section" class="hidden mb-8 rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
            <div class="bg-gradient-to-r from-teal-100 to-lime-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa-check-circle mr-2 text-green-600"></i> 3. Meal Plan and Grocery List
                </h2>
            </div>
            <div class="p-6">
                <div id="meal-plan" class="mb-6 rounded-lg bg-white/80 p-4 shadow-sm">
                    <h3 class="text-lg font-semibold text-gray-700 mb-3 flex items-center">
                        <i class="fas fa-calendar-alt mr-2 text-blue-500"></i> Meal Plan:
                    </h3>
                    <div id="meal-plan-content" class="bg-gray-100 border rounded-md p-4 text-gray-800"></div>
                </div>
                <div id="grocery-list" class="mb-6 rounded-lg bg-white/80 p-4 shadow-sm">
                    <h3 class="text-lg font-semibold text-gray-700 mb-3 flex items-center">
                        <i class="fas fa-shopping-cart mr-2 text-purple-500"></i> Grocery List:
                    </h3>
                    <div id="grocery-list-content" class="bg-gray-100 border rounded-md p-4 text-gray-800"></div>
                </div>
            </div>
        </div>
        <div id="recipe-section" class="hidden rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
             <div class="bg-gradient-to-r from-orange-100 to-yellow-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                <i class="fas fa-lightbulb mr-2 text-orange-500"></i> 4. Recipe Suggestions
                </h2>
            </div>
            <div class="p-6">
                <div id="recipe-suggestions" class="rounded-lg bg-white/80 p-4 shadow-sm">
                    <h3 class="text-lg font-semibold text-gray-700 mb-3 flex items-center">
                         <i class="fas fa-book-open mr-2 text-pink-500"></i> Recipe Suggestions:
                    </h3>
                    <div id="recipe-suggestions-content" class="bg-gray-100 border rounded-md p-4 text-gray-800"></div>
                </div>
            </div>
        </div>
        <div id="progress-section" class="hidden mb-8 rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
            <div class="bg-gradient-to-r from-purple-100 to-pink-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa-chart-line mr-2 text-purple-600"></i> 5. Your Progress
                </h2>
            </div>
            <div class="p-6">
                <div id="progress-chart-container" class="rounded-lg bg-white/80 p-4 shadow-sm">
                    <h3 class="text-lg font-semibold text-gray-700 mb-3 flex items-center">
                        <i class="fas fa-chart-bar mr-2 text-green-500"></i> Daily Progress
                    </h3>
                    <canvas id="progress-chart-canvas"></canvas>
                </div>
            </div>
        </div>
        <div id="inventory-tracking-section" class="mb-8 rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
            <div class="bg-gradient-to-r from-purple-200 to-blue-200 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa- холодильник mr-2 text-purple-700"></i> 6. Real-time Inventory Tracking
                </h2>
            </div>
            <div class="p-6">
                <p class="text-gray-700 mb-4">
                    Use image recognition to scan and track your pantry and fridge contents.  Get meal suggestions based on what's available to minimize food waste.
                </p>
                <button id="start-scan-button" class="bg-gradient-to-r from-indigo-500 to-violet-600 hover:from-indigo-600 hover:to-violet-700 text-white font-bold py-3 px-6 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                    <i class="fas fa-camera mr-2"></i> Start Scan
                </button>
                <div id="camera-feed" class="mt-4 rounded-md overflow-hidden border border-dashed border-gray-400 hidden">
                    <video id="video" class="w-full" autoplay></video>
                </div>
                <div id="inventory-list" class="mt-6 rounded-lg bg-white/80 p-4 shadow-sm">
                    <h3 class="text-lg font-semibold text-gray-700 mb-3 flex items-center">
                        <i class="fas fa-list-ul mr-2 text-blue-600"></i> Your Inventory:
                    </h3>
                    <ul id="inventory-items" class="list-none p-0">
                        {/* Inventory items will be added here */}
                    </ul>
                </div>
                <div id="meal-suggestions-inventory" class="mt-6 rounded-lg bg-white/80 p-4 shadow-sm hidden">
                    <h3 class="text-lg font-semibold text-gray-700 mb-3 flex items-center">
                        <i class="fas fa-lightbulb mr-2 text-orange-500"></i> Meal Suggestions:
                    </h3>
                    <div id="meal-suggestions-content-inventory" class="bg-gray-100 border rounded-md p-4 text-gray-800">
                        {/* Meal suggestions based on inventory will be displayed here */}
                    </div>
                </div>
            </div>
        </div>
        <div id="grocery-integration-section" class="mb-8 rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
            <div class="bg-gradient-to-r from-pink-100 to-yellow-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa-shopping-bag mr-2 text-pink-600"></i> 7. Grocery Service Integration
                </h2>
            </div>
            <div class="p-6">
                <p class="text-gray-700 mb-4">
                    Integrate with grocery delivery services to easily order the ingredients for your meal plan.
                </p>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <button class="bg-pink-600 hover:bg-pink-700 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center justify-center">
                        <img src="https://www.instacart.com/images/favicon.ico" alt="Instacart" class="mr-2 w-6 h-6 rounded-full"> Instacart
                    </button>
                    <button class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center justify-center">
                        <img src="https://www.freshdirect.com/favicon.ico" alt="FreshDirect" class="mr-2 w-6 h-6 rounded-full"> Fresh Direct
                    </button>
                </div>
                <p class="text-gray-500 mt-4 text-sm">
                    Note: Integration may require linking your accounts and may not be available in all areas.
                </p>
            </div>
        </div>
        <div id="hydration-section" class="mb-8 rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
            <div class="bg-gradient-to-r from-cyan-100 to-blue-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa-tint mr-2 text-cyan-600"></i> 8. Hydration Tracking
                </h2>
            </div>
            <div class="p-6">
                <p class="text-gray-700 mb-4">
                   Enable daily reminders to drink water and stay hydrated.
                </p>
                <button id="toggle-hydration-button" class="bg-gradient-to-r from-orange-400 to-red-500 hover:from-orange-500 hover:to-red-600 text-white font-bold py-3 px-6 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                    <i class="fas fa-bell mr-2"></i> Enable Hydration Alerts
                </button>
                <p id="hydration-status" class="mt-4 text-gray-600">Hydration alerts are currently disabled.</p>
            </div>
        </div>
        <div id="social-sharing-section" class="mb-8 rounded-lg shadow-sm bg-gradient-to-br from-gray-50 to-white/50 backdrop-blur-md">
            <div class="bg-gradient-to-r from-indigo-100 to-purple-100 p-6 rounded-t-lg">
                <h2 class="text-2xl font-semibold text-gray-800 flex items-center">
                    <i class="fas fa-share-alt mr-2 text-indigo-600"></i> 9. Share Your Progress
                </h2>
            </div>
            <div class="p-6">
                <p class="text-gray-700 mb-4">
                    Share your meal plans, recipes, and progress with your friends and followers on social media.
                </p>
                <div class="flex justify-center space-x-4">
                    <button id="share-facebook" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                        <i class="fab fa-facebook-f mr-2"></i> Share on Facebook
                    </button>
                    <button id="share-instagram" class="bg-pink-500 hover:bg-pink-600 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                        <i class="fab fa-instagram mr-2"></i> Share on Instagram
                    </button>
                    <button id="share-twitter" class="bg-sky-400 hover:bg-sky-500 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 flex items-center">
                        <i class="fab fa-x-twitter mr-2"></i> Share on X
                    </button>
                </div>
            </div>
        </div>
    </div>
    <script>
        const goals = {};
        const recipes = [
  {
    name: "Vegan Lentil Soup",
    ingredients: [
      "1 cup red lentils",
      "1 onion, chopped",
      "2 carrots, diced",
      "2 celery stalks, diced",
      "4 cups vegetable broth",
      "1 tsp cumin",
      "Salt and pepper to taste",
      "Whole-grain bread, for serving"
    ],
    instructions: [
      "Rinse the lentils.",
      "Sauté the onion, carrots, and celery in a pot.",
      "Add the lentils, broth, cumin, salt, and pepper.",
      "Simmer until lentils are tender.",
      "Serve hot with whole-grain bread."
    ],
    dietaryRestrictions: ["vegan", "gluten-free"],
    nutrition: {
      calories: 350,
      protein: 20,
      carbs: 60,
      fat: 8,
      fiber: 15,
      sugar: 5
    },
    preferences: ["hearty", "healthy", "soup"]
  },
  {
    name: "Baked Chicken Breast",
    ingredients: [
      "2 boneless, skinless chicken breasts",
      "1 tbsp olive oil",
      "1 tsp paprika",
      "1 tsp garlic powder",
      "Salt and pepper to taste",
      "Roasted vegetables (broccoli, carrots), for serving"
    ],
    instructions: [
      "Preheat oven to 375°F (190°C).",
      "Drizzle chicken breasts with olive oil.",
      "Season with paprika, garlic powder, salt, and pepper.",
      "Bake for 20-25 minutes, or until cooked through.",
      "Serve with roasted vegetables."
    ],
    dietaryRestrictions: ["gluten-free"],
    nutrition: {
      calori
