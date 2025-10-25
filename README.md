/**
 * POLYLITHICAI PLATFORM (Simplified Single-File Demo)
 * ---------------------------------------------------
 * This is a minimal runnable simulation of the PolylithicAI project.
 * It spins up a small Express backend with multiple endpoints and
 * serves a static frontend page — all from one file.
 */

import express from "express";
import path from "path";
import { fileURLToPath } from "url";

// ------------------------------------------------------
// Environment setup
// ------------------------------------------------------
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const app = express();
app.use(express.json());

// ------------------------------------------------------
// Simulated in-memory databases
// ------------------------------------------------------
let models = [];
let users = [
  { id: 1, name: "Admin", role: "admin" },
  { id: 2, name: "Data Scientist", role: "developer" }
];

// ------------------------------------------------------
// Root route (project info)
// ------------------------------------------------------
app.get("/", (req, res) => {
  res.send(`
    <h1>🧠 PolylithicAI Platform</h1>
    <p>Welcome to PolylithicAI — a modular MLOps & SaaS demo system.</p>
    <p>Available routes:</p>
    <ul>
      <li>GET /api/users</li>
      <li>GET /api/models</li>
      <li>POST /api/models</li>
      <li>GET /api/pipelines</li>
      <li>POST /api/pipelines/run</li>
    </ul>
  `);
});

// ------------------------------------------------------
// User management endpoints
// ------------------------------------------------------
app.get("/api/users", (req, res) => {
  res.json(users);
});

// ------------------------------------------------------
// Model registry endpoints
// ------------------------------------------------------
app.get("/api/models", (req, res) => {
  res.json(models);
});

app.post("/api/models", (req, res) => {
  const { name, version, artifactUrl } = req.body;
  if (!name || !version || !artifactUrl) {
    return res.status(400).json({ error: "Missing fields" });
  }
  const newModel = { id: models.length + 1, name, version, artifactUrl, status: "registered" };
  models.push(newModel);
  res.status(201).json(newModel);
});

// ------------------------------------------------------
// Pipeline orchestration endpoints
// ------------------------------------------------------
app.get("/api/pipelines", (req, res) => {
  res.json([
    { id: 1, name: "daily-churn-pipeline", status: "ready" },
    { id: 2, name: "fraud-detection-pipeline", status: "paused" }
  ]);
});

app.post("/api/pipelines/run", (req, res) => {
  const { pipelineId } = req.body;
  if (!pipelineId) {
    return res.status(400).json({ error: "Missing pipelineId" });
  }
  res.json({ message: `Pipeline ${pipelineId} triggered successfully` });
});

// ------------------------------------------------------
// Minimal static frontend (served from memory)
// ------------------------------------------------------
app.get("/dashboard", (req, res) => {
  res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <title>PolylithicAI Dashboard</title>
        <style>
          body { font-family: Arial, sans-serif; background: #f9fafb; color: #222; padding: 2rem; }
          h1 { color: #2563eb; }
          .card { background: white; padding: 1.5rem; border-radius: 1rem; box-shadow: 0 2px 6px rgba(0,0,0,0.1); margin-bottom: 1rem; }
          code { background: #e2e8f0; padding: 0.2rem 0.4rem; border-radius: 0.3rem; }
          .btn { display: inline-block; padding: 0.5rem 1rem; background: #2563eb; color: white; border-radius: 0.4rem; text-decoration: none; }
        </style>
      </head>
      <body>
        <h1>PolylithicAI Dashboard</h1>
        <div class="card">
          <h2>Project Overview</h2>
          <p>This dashboard represents a simplified front-end for the PolylithicAI platform.</p>
        </div>
        <div class="card">
          <h2>Try the API</h2>
          <p>GET <code>/api/models</code> → List registered models</p>
          <p>POST <code>/api/models</code> → Register new model</p>
          <p>GET <code>/api/pipelines</code> → View available pipelines</p>
        </div>
        <a href="/" class="btn">Back to API Root</a>
      </body>
    </html>
  `);
});

// ------------------------------------------------------
// Server listener
// ------------------------------------------------------
const PORT = process.env.PORT || 4000;
app.listen(PORT, () => {
  console.log(`🚀 PolylithicAI server running on http://localhost:${PORT}`);
  console.log(`💡 Open dashboard at http://localhost:${PORT}/dashboard`);
});
