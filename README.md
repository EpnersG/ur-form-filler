# ur-form-filler
├── frontend
│   ├── public
│   │   └── index.html
│   ├── src
│   │   ├── components
│   │   │   ├── Home.js
│   │   │   └── FormFiller.js
│   │   ├── App.js
│   │   ├── index.js
│   │   └── constants.js
│   ├── package.json
│   └── README.md
├── backend
│   ├── server.js
│   ├── .env
│   ├── package.json
│   └── README.md
└── README.md
{
  "name": "ur-form-filler-frontend",
  "version": "1.0.0",
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build"
  },
  "dependencies": {
    "axios": "^1.5.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.17.0"
  }
}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>UR Form Filler</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root")
);
import React from "react";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import Home from "./components/Home";
import FormFiller from "./components/FormFiller";

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/form-filler" element={<FormFiller />} />
      </Routes>
    </Router>
  );
}

export default App;
import React from "react";
import { Link } from "react-router-dom";

const Home = () => (
  <div>
    <h1>UR Form Filler</h1>
    <p>Aizpildiet Uzņēmumu reģistra veidlapas viegli un ātri.</p>
    <Link to="/form-filler">Sākt</Link>
  </div>
);

export default Home;
import React, { useState } from "react";
import axios from "axios";
import { BACKEND_URL } from "../constants";

const FormFiller = () => {
  const [regNum, setRegNum] = useState("");
  const [data, setData] = useState(null);

  const fetchCompanyData = async () => {
    try {
      const response = await axios.get(`${BACKEND_URL}/company/${regNum}`);
      setData(response.data);
    } catch (error) {
      console.error("Kļūda, iegūstot uzņēmuma datus", error);
    }
  };

  return (
    <div>
      <h2>Aizpildīt veidlapu</h2>
      <input
        type="text"
        placeholder="Ievadiet uzņēmuma reģistrācijas numuru"
        value={regNum}
        onChange={(e) => setRegNum(e.target.value)}
      />
      <button onClick={fetchCompanyData}>Meklēt datus</button>
      {data && (
        <div>
          <h3>Uzņēmuma dati:</h3>
          <p>Nosaukums: {data.name}</p>
          <p>Adrese: {data.address}</p>
        </div>
      )}
    </div>
  );
};

export default FormFiller;
export const BACKEND_URL = "http://localhost:5000/api";
{
  "name": "ur-form-filler-backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "axios": "^1.5.0",
    "body-parser": "^1.20.2",
    "dotenv": "^16.3.0",
    "express": "^4.18.2",
    "openai": "^3.2.1"
  }
}
const express = require("express");
const bodyParser = require("body-parser");
const axios = require("axios");
const { Configuration, OpenAIApi } = require("openai");
require("dotenv").config();

const app = express();
app.use(bodyParser.json());

// OpenAI konfigurācija
const openai = new OpenAIApi(
  new Configuration({
    apiKey: process.env.OPENAI_API_KEY,
  })
);

// Maršruts uzņēmumu datu iegūšanai
app.get("/api/company/:regNum", async (req, res) => {
  const { regNum } = req.params;
  try {
    // Šeit ievietot reālu info.ur.gov.lv API pieprasījumu
    const mockResponse = { name: "Test SIA", address: "Rīga, Brīvības iela 1" };
    res.json(mockResponse);
  } catch (error) {
    res.status(500).send("Kļūda, iegūstot uzņēmuma datus");
  }
});

// Maršruts veidlapas ģenerēšanai
app.post("/api/generate-form", async (req, res) => {
  const { companyData } = req.body;
  try {
    const prompt = `
      Izveido veidlapu uzņēmumam ar šādiem datiem:
      Nosaukums: ${companyData.name}
      Adrese: ${companyData.address}
      Formāts: teksts latviešu valodā.
    `;
    const aiResponse = await openai.createCompletion({
      model: "text-davinci-003",
      prompt,
      max_tokens: 500,
    });
    res.json({ form: aiResponse.data.choices[0].text });
  } catch (error) {
    res.status(500).send("Kļūda, ģenerējot veidlapu");
  }
});

// Servera palaišana
app.listen(5000, () => console.log("Serveris darbojas portā 5000"));
OPENAI_API_KEY=YOUR_API_KEY_HERE
git init
git add .
git commit -m "Initial commit for UR Form Filler"
