# Farm-managers
Thus is for farm bissness data save, calculation and  bissnes grouth

farm-manager/
│── package.json
│── vite.config.js
│── index.html
│── tailwind.config.js
│── postcss.config.js
│── README.md
│
├── public/
│   └── favicon.ico
│
└── src/
    ├── App.jsx
    ├── main.jsx
    ├── index.css
    └── components/
        ├── Dashboard.jsx
        ├── FarmList.jsx
        ├── FarmForm.jsx
        └── HarvestForm.jsx


{
  "name": "farm-manager",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.3.2",
    "autoprefixer": "^10.4.20",
    "postcss": "^8.4.47",
    "tailwindcss": "^3.4.13",
    "vite": "^5.4.10"
  }
}

import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
})

/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}

export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Farm Manager</title>
  </head>
  <body class="bg-gray-100">
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>

@tailwind base;
@tailwind components;
@tailwind utilities;

body {
  font-family: Arial, sans-serif;
}

import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)

import React, { useState } from "react";
import Dashboard from "./components/Dashboard";
import FarmList from "./components/FarmList";
import FarmForm from "./components/FarmForm";
import HarvestForm from "./components/HarvestForm";

export default function App() {
  const [farms, setFarms] = useState([]);

  const addFarm = (farm) => {
    setFarms([...farms, { ...farm, id: Date.now(), harvests: [] }]);
  };

  const deleteFarm = (id) => {
    setFarms(farms.filter((f) => f.id !== id));
  };

  const addHarvest = (farmId, harvest) => {
    setFarms(
      farms.map((f) =>
        f.id === farmId
          ? { ...f, harvests: [...f.harvests, { ...harvest, id: Date.now() }] }
          : f
      )
    );
  };

  return (
    <div className="max-w-3xl mx-auto p-4">
      <h1 className="text-3xl font-bold text-center mb-4">🌱 Farm Manager</h1>
      <FarmForm onAddFarm={addFarm} />
      <Dashboard farms={farms} />
      <FarmList farms={farms} onDeleteFarm={deleteFarm} onAddHarvest={addHarvest} />
    </div>
  );
}

import React, { useState } from "react";

export default function FarmForm({ onAddFarm }) {
  const [name, setName] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!name) return;
    onAddFarm({ name });
    setName("");
  };

  return (
    <form onSubmit={handleSubmit} className="flex gap-2 mb-4">
      <input
        type="text"
        placeholder="Farm name"
        value={name}
        onChange={(e) => setName(e.target.value)}
        className="border rounded p-2 flex-1"
      />
      <button type="submit" className="bg-green-600 text-white px-4 rounded">
        Add Farm
      </button>
    </form>
  );
}

import React from "react";
import HarvestForm from "./HarvestForm";

export default function FarmList({ farms, onDeleteFarm, onAddHarvest }) {
  return (
    <div className="space-y-4">
      {farms.map((farm) => (
        <div key={farm.id} className="bg-white shadow rounded p-4">
          <div className="flex justify-between items-center">
            <h2 className="text-xl font-bold">{farm.name}</h2>
            <button
              onClick={() => onDeleteFarm(farm.id)}
              className="text-red-600"
            >
              ❌ Delete
            </button>
          </div>

          <HarvestForm farmId={farm.id} onAddHarvest={onAddHarvest} />

          <ul className="mt-2 space-y-1">
            {farm.harvests.map((h) => (
              <li key={h.id} className="border p-2 rounded">
                <strong>{h.date}</strong> | Prod: {h.production} | Cost: {h.cost} | Sale: {h.sale} | Profit:{" "}
                <span className={h.sale - h.cost >= 0 ? "text-green-600" : "text-red-600"}>
                  {h.sale - h.cost}
                </span>
              </li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
}

import React, { useState } from "react";

export default function HarvestForm({ farmId, onAddHarvest }) {
  const [form, setForm] = useState({
    date: "",
    production: "",
    cost: "",
    sale: "",
  });

  const handleChange = (e) => {
    setForm({ ...form, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!form.date || !form.production) return;
    onAddHarvest(farmId, {
      date: form.date,
      production: Number(form.production),
      cost: Number(form.cost),
      sale: Number(form.sale),
    });
    setForm({ date: "", production: "", cost: "", sale: "" });
  };

  return (
    <form onSubmit={handleSubmit} className="flex gap-2 mt-2">
      <input
        type="date"
        name="date"
        value={form.date}
        onChange={handleChange}
        className="border rounded p-1"
      />
      <input
        type="number"
        name="production"
        placeholder="Prod"
        value={form.production}
        onChange={handleChange}
        className="border rounded p-1 w-20"
      />
      <input
        type="number"
        name="cost"
        placeholder="Cost"
        value={form.cost}
        onChange={handleChange}
        className="border rounded p-1 w-20"
      />
      <input
        type="number"
        name="sale"
        placeholder="Sale"
        value={form.sale}
        onChange={handleChange}
        className="border rounded p-1 w-20"
      />
      <button type="submit" className="bg-blue-600 text-white px-2 rounded">
        +
      </button>
    </form>
  );
}

import React from "react";

export default function Dashboard({ farms }) {
  const totalProduction = farms.reduce(
    (sum, f) => sum + f.harvests.reduce((s, h) => s + h.production, 0),
    0
  );
  const totalCost = farms.reduce(
    (sum, f) => sum + f.harvests.reduce((s, h) => s + h.cost, 0),
    0
  );
  const totalSale = farms.reduce(
    (sum, f) => sum + f.harvests.reduce((s, h) => s + h.sale, 0),
    0
  );
  const profit = totalSale - totalCost;

  return (
    <div className="bg-white shadow p-4 rounded mb-4">
      <h2 className="text-2xl font-bold mb-2">📊 Dashboard</h2>
      <p>Total Production: {totalProduction}</p>
      <p>Total Cost: {totalCost}</p>
      <p>Total Sale: {totalSale}</p>
      <p>
        Profit/Loss:{" "}
        <span className={profit >= 0 ? "text-green-600" : "text-red-600"}>
          {profit}
        </span>
      </p>
    </div>
  );
}


npm install
npm run dev