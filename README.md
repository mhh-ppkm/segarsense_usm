# SegarSense – Chicken Freshness Scanner

SegarSense is a smartphone web app that reads a colour-changing freshness indicator label on packaged chicken. It corrects the photo's colour with a white reference patch, converts it to CIELAB, measures colour change with CIEDE2000 (ΔE₀₀), and classifies the chicken as **Fresh**, **Semi-fresh** or **Spoiled** using a model you train from your own lab data.

Developed at the School of Mechanical Engineering, Universiti Sains Malaysia (USM).

**Live app:** `https://mhh-ppkm.github.io/segarsense/` (after you enable GitHub Pages, see below)

## Features

| Tab | What it does |
| --- | --- |
| Scan | Take or choose a photo, tap the indicator (P) and the white patch (W), and get the freshness class, ΔE₀₀, L\*a\*b\*, hue and an estimated TVB-N. |
| Data & model | Stores labelled readings, trains a weighted kNN classifier, shows leave-one-out accuracy, a confusion matrix, TVB-N regression R² and an a\*–b\* plot. Import and export CSV. |
| Label design | Generates a printable 60 × 40 mm label with an indicator window, white and grey calibration patches and reference colours. Downloads as SVG. |
| Guide | Experimental workflow, how the algorithm works, and references. |

Supported indicator dyes: butterfly pea (*Clitoria ternatea*) anthocyanin, roselle (*Hibiscus sabdariffa*) anthocyanin, curcumin, and a custom option that relies fully on your trained data.

## Project structure

```
segarsense/
├── index.html              # The whole app (HTML, CSS, JS in one file)
├── manifest.webmanifest    # Lets phones install it as an app
├── sw.js                   # Service worker for offline use
├── icons/                  # App icons (192 and 512 px)
├── samples/
│   ├── segarsense_simulated_samples.csv   # 32 simulated labelled readings
│   └── test-images/                       # 8 simulated label photos (batch B5)
├── .nojekyll
├── LICENSE
└── README.md
```

## Deploy on GitHub Pages

1. Create a new repository named `segarsense` under your GitHub account.
2. Upload every file and folder in this package to the repository root (keep the folder structure).
3. Go to **Settings → Pages**, set **Source** to *Deploy from a branch*, choose `main` and `/ (root)`, then **Save**.
4. After about a minute the app is live at `https://<your-username>.github.io/segarsense/`.
5. On Android Chrome, open the link and choose **⋮ → Add to Home screen** to install it like an app.

GitHub Pages serves over HTTPS, so the camera, gallery picker, live camera and offline mode all work in Chrome and Safari.

## Try it with the sample data

1. Open the app and choose **Butterfly pea** as the indicator dye.
2. In **Data & model**, tap **Import CSV** and select `samples/segarsense_simulated_samples.csv`.
3. In **Scan**, choose a photo from `samples/test-images/`, tap the centre of the indicator circle, then the WHITE patch.
4. Expected results: day 0–3 Fresh, day 4–6 Semi-fresh, day 7–8 Spoiled.
5. Delete the simulated readings before starting a real experiment.

> **Note:** all sample data is **simulated** for software testing. It is not laboratory data, and the preset reference colours are approximations from the literature. Calibrate with your own film before reporting results.

## Data and privacy

On GitHub Pages, readings are saved in the browser's local storage on that device only. Nothing is uploaded to a server. Clearing browser data deletes the readings, so export a CSV regularly as a backup.

### CSV format

`id, ts, batch, day, dye, truth, pred, tvbn, ph, temp, R, G, B, Rc, Gc, Bc, L, a, b, note`

- `dye`: `telang`, `roselle`, `kurkumin` or `custom`
- `truth` / `pred`: `segar` (fresh), `separa` (semi-fresh), `rosak` (spoiled) or blank
- `R G B`: raw colour, `Rc Gc Bc`: white-corrected colour, `L a b`: CIELAB (D65)

## How it works

1. **White balance:** each channel is scaled so the white patch reads 242 (adjustable).
2. **Colour space:** sRGB → linear RGB → XYZ (D65) → CIELAB.
3. **Colour difference:** CIEDE2000, checked against the Sharma et al. (2005) test data.
4. **Classification:** weighted kNN (k = 3 by default) once two or more classes have two or more labelled readings; otherwise preset reference colours or ΔE₀₀ thresholds against the earliest day in the batch.
5. **TVB-N estimate:** multiple linear regression on a\* and b\* once four or more readings have TVB-N values.

## References

1. Guo L. et al. (2020). Portable food-freshness prediction platform based on colorimetric barcode combinatorics and deep convolutional neural networks. *Advanced Materials*, 32(45), 2004805.
2. Varsha S.C. et al. (2026). Harnessing machine learning for chicken meat quality evaluation using curcumin-loaded methylcellulose intelligent films. *Sustainable Food Technology*, RSC.
3. Lee K.E. et al. (2019). A freshness indicator for monitoring chicken-breast spoilage using a Tyvek® sheet and RGB color analysis. *Food Packaging and Shelf Life*, 19, 40–46.
4. Lee K.E. et al. (2019). Colorimetric array freshness indicator and digital color processing for monitoring the freshness of packaged chicken breast. *Food Packaging and Shelf Life*, 22, 100408.
5. Ghorbanizamani F. (2025). Chicken meat spoilage detection using color-shifting silver nanoparticles, smartphone imaging, and ANN. *Food Chemistry*, 468, 142390.
6. Sharma G., Wu W., Dalal E.N. (2005). The CIEDE2000 color-difference formula: implementation notes, supplementary test data, and mathematical observations. *Color Research & Application*, 30(1), 21–30.

## Author

Ir. Ts. Dr. Muhammad Hafiz bin Hassan
School of Mechanical Engineering, Universiti Sains Malaysia
ORCID: [0000-0002-8717-8973](https://orcid.org/0000-0002-8717-8973)

## License

MIT. See [LICENSE](LICENSE).
