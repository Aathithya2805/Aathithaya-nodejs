const express = require("express");
const multer = require("multer");
const cors = require("cors");
const fs = require("fs");
const path = require("path");

const app = express();
app.use(cors());
app.use(express.static("uploads"));

const storage = multer.diskStorage({
  destination: "uploads/",
  filename: (req, file, cb) => {
    cb(null, Date.now() + path.extname(file.originalname));
  },
});

const upload = multer({ storage });

// Upload endpoint
app.post("/upload", upload.single("file"), (req, res) => {
  res.json({ message: "File uploaded successfully", filename: req.file.filename });
});

// Get all files
app.get("/files", (req, res) => {
  fs.readdir("uploads", (err, files) => {
    if (err) return res.status(500).json({ error: "Error reading files" });
    res.json(files);
  });
});

// Delete file
app.delete("/files/:name", (req, res) => {
  const filePath = path.join(__dirname, "uploads", req.params.name);
  fs.unlink(filePath, (err) => {
    if (err) return res.status(500).json({ error: "File not found" });
    res.json({ message: "File deleted successfully" });
  });
});

const PORT = 5000;
app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
