# 📄 Web-Based Intelligent Document Scanner & Perspective Rectifier

An interactive, browser-based computer vision application that transforms skewed, angled document photos taken from a comfortable horizontal position into perfectly flattened, crisp, top-down digital documents. Powered by **OpenCV.js (WebAssembly)** and native HTML5 Canvas APIs, this tool replicates the core geometry rectification engines found in commercial scanning applications like Adobe Scan and CamScanner directly in the browser.

---

## 🚀 Core Problem Solved

When capturing photos of documents, notebooks, or receipts resting flat on a table, holding the phone perfectly parallel vertically is physically strainful and causes harsh shadows. The natural alternative is holding the phone at a comfortable angled slant. However, this introduces severe **perspective distortion (the trapezoid effect)** where the top of the paper appears narrower and further away than the bottom.

This application solves this problem natively in the browser:
1. **Uploads any image size** cleanly, maintaining aspect ratios through relative viewport scaling.
2. Allows the user to click or select the four corners of the document in **any arbitrary sequence**.
3. **Automatically sorts coordinates** mathematically to prevent orientation inversion or image flipping.
4. Uses a **3×3 Homography Matrix Transformation** via WebAssembly to warp, crop, and flatten the document into a crisp, standard A4 aspect ratio rectangle.

---

## ✨ Key Features

- **Asynchronous WebAssembly Core:** Leverages a lightweight, self-contained `opencv.js` engine to execute intensive matrix transformations in milliseconds.
- **Foolproof Coordinate Auto-Sorting:** Implements a deterministic geographic sorting algorithm. Users can click corners in any completely random sequence—the interface automatically resolves **Top-Left (TL), Top-Right (TR), Bottom-Right (BR), and Bottom-Left (BL)** orientations seamlessly.
- **Dynamic Viewport Scaling:** Preserves high-resolution properties while adapting oversized mobile images down to a manageable 500px interactive canvas layout.
- **Automatic Memory Cleanup:** Programmatically releases WebAssembly vector heaps (`.delete()`) after every execution pass to avoid browser tab memory leaks.
- **Pure Client-Side Processing:** Zero telemetry, zero server-side uploads. Your documents are processed entirely within local sandbox memory, ensuring maximum corporate data privacy.

---

## 🛠️ Technology Stack

- **Core Engine:** OpenCV.js (v4.5.4) compiled to WebAssembly.
- **Frontend UI:** HTML5, Semantic CSS3 (Apple Design Language typography styling).
- **Graphics Pipeline:** Double HTML5 `<canvas>` Context elements (`srcCanvas` for coordinate mapping, `dstCanvas` for matrix rendering).

---

## 🧠 Technical Under the Hood: How the Logic Works

### User Sequence Coordinate Sorting Logic
To give the user total freedom to click corners randomly, the application passes points through a coordinate split pipeline before sending them to WebAssembly:
- Points are sorted on the $X$-axis to divide them cleanly into a **Left Hemisphere** and a **Right Hemisphere**.
- The Left Hemisphere elements are sorted on the $Y$-axis to definitively separate the **Top-Left (TL)** from the **Bottom-Left (BL)**.
- The Right Hemisphere elements are sorted on the $Y$-axis to separate the **Top-Right (TR)** from the **Bottom-Right (BR)**.

```javascript
function sortPoints(points) {
    const sortedByX = [...points].sort((a, b) => a.x - b.x);
    const leftPoints = sortedByX.slice(0, 2);
    const rightPoints = sortedByX.slice(2, 4);

    const [tl, bl] = leftPoints.sort((a, b) => a.y - b.y);
    const [tr, br] = rightPoints.sort((a, b) => a.y - b.y);

    return [tl, tr, br, bl]; // Guaranteed correct order for cv.getPerspectiveTransform
}



"Initial README.md deatils"
