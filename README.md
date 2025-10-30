# fullstack5.1
// crudApp.js
const express = require("express");
const mongoose = require("mongoose");
const bodyParser = require("body-parser");

const app = express();
app.use(bodyParser.json());

// ========================================
// 1️⃣ CONNECT TO MONGODB
// ========================================
mongoose
  .connect("mongodb://127.0.0.1:27017/crudDemoDB")
  .then(() => console.log("✅ Connected to MongoDB"))
  .catch((err) => console.error("❌ MongoDB Connection Error:", err));

// ========================================
// 2️⃣ DEFINE SCHEMA AND MODEL
// ========================================
const productSchema = new mongoose.Schema({
  name: { type: String, required: true },
  category: String,
  price: Number,
  inStock: Boolean,
});

const Product = mongoose.model("Product", productSchema);

// ========================================
// 3️⃣ CRUD OPERATIONS (API ROUTES)
// ========================================

// ➕ CREATE
app.post("/products", async (req, res) => {
  try {
    const newProduct = new Product(req.body);
    await newProduct.save();
    res.status(201).json({ message: "✅ Product created!", product: newProduct });
  } catch (error) {
    res.status(500).json({ message: "❌ Error creating product", error });
  }
});

// 📋 READ ALL
app.get("/products", async (req, res) => {
  try {
    const products = await Product.find();
    res.json(products);
  } catch (error) {
    res.status(500).json({ message: "❌ Error fetching products", error });
  }
});

// 🔍 READ ONE BY ID
app.get("/products/:id", async (req, res) => {
  try {
    const product = await Product.findById(req.params.id);
    if (!product) return res.status(404).json({ message: "❌ Product not found" });
    res.json(product);
  } catch (error) {
    res.status(500).json({ message: "❌ Error fetching product", error });
  }
});

// ✏️ UPDATE
app.put("/products/:id", async (req, res) => {
  try {
    const updated = await Product.findByIdAndUpdate(req.params.id, req.body, {
      new: true,
    });
    if (!updated) return res.status(404).json({ message: "❌ Product not found" });
    res.json({ message: "✅ Product updated!", updated });
  } catch (error) {
    res.status(500).json({ message: "❌ Error updating product", error });
  }
});

// ❌ DELETE
app.delete("/products/:id", async (req, res) => {
  try {
    const deleted = await Product.findByIdAndDelete(req.params.id);
    if (!deleted) return res.status(404).json({ message: "❌ Product not found" });
    res.json({ message: "✅ Product deleted successfully!" });
  } catch (error) {
    res.status(500).json({ message: "❌ Error deleting product", error });
  }
});

// ========================================
// 4️⃣ HOME ROUTE (HELP PAGE)
// ========================================
app.get("/", (req, res) => {
  res.send(`
    <h2>🛒 MongoDB CRUD Demo (Node.js + Mongoose)</h2>
    <p>Available Endpoints:</p>
    <ul>
      <li>POST /products → Create new product</li>
      <li>GET /products → Get all products</li>
      <li>GET /products/:id → Get product by ID</li>
      <li>PUT /products/:id → Update product by ID</li>
      <li>DELETE /products/:id → Delete product by ID</li>
    </ul>
  `);
});

// ========================================
// 5️⃣ START SERVER
// ========================================
const PORT = 4000;
app.listen(PORT, () => console.log(`🚀 Server running at http://localhost:${PORT}`));
