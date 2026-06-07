# CD31 TWS Area Macro

A Fiji/ImageJ macro (BeanShell) for **semi-automatic batch quantification of CD31 IHC** images. For each image it measures how much of a drawn region of interest (ROI) is CD31-positive, using a pre-trained Trainable Weka Segmentation (TWS) classifier.

The reported value per image is:

```
CD31.Ar/T.Ar (%) = total CD31-positive area / ROI area × 100
```

The macro reproduces the manual Fiji workflow "Create Result → threshold the CD31 class", just batched and logged so results are reproducible.

## Requirements

- [Fiji](https://imagej.net/software/fiji/) (ImageJ with the **Trainable Weka Segmentation** plugin, bundled by default).
- A trained TWS classifier file (`.model`).
- Images and matching ROI files (see below).

## Inputs

You provide four things when the macro runs:

1. **Image folder** — the original images. Supported: `.tif`, `.tiff`, `.jpg`, `.jpeg`, `.png`, `.bmp`.
2. **ROI folder** — one `.roi` file per image, named with the **same base name** as the image.
   Example: `sample01.tif` → `sample01.roi`. Images without a matching ROI are skipped.
3. **Classifier** — the trained TWS `.model` file.
4. **Output folder** — where results are written.

## How to run

1. Open Fiji.
2. `File ▸ Open…` and select `cd31_tws_area_makro.bsh` (Fiji opens it in the Script Editor with BeanShell selected, because of the `.bsh` extension).
3. Click **Run**.
4. Pick the four folders/files in the dialogs that appear.
5. In the parameter dialog, set the values (see below), then click **OK**.
6. Wait for the run to finish — a summary dialog reports how many images were processed and skipped.

## Parameters

| Parameter | Default | Meaning |
|---|---|---|
| `Run_Name` | `CD31_TWS_CreateResult_Run` | Label used in the output folder name and logged with every row. |
| `CD31_Result_Class_Value` | `0` | The class index (in the classified result image) that represents CD31. **Must match how the classifier was trained** — see note below. |
| `Pixel_width_um` / `Pixel_height_um` | `1.02` | Physical pixel size in micrometres. |
| `Overwrite_image_calibration_with_values_above` | on | If on, the pixel size above is forced onto every image. If off, the calibration stored in each image is used. |
| `Save_CD31_raw_masks` | on | Save the black/white CD31 mask per image. |
| `Save_QC_overlays` | on | Save a quality-control overlay (original image with ROI outline and CD31 borders drawn on top). |

## Outputs

Each run creates a timestamped folder `<output>/<timestamp>_<Run_Name>/` containing:

- `CD31_TWS_Summary.csv` — one row per image with all measured values (the main result).
- `TWS_classified_results/` — the raw classified label image per input.
- `CD31_raw_masks/` — black/white CD31 masks (if enabled).
- `QC_overlays/` — visual overlays for checking the result by eye (if enabled).
- `CD31_TWS_Log.txt` — run parameters and a per-image processing log.

## Important note on the CD31 class

`CD31_Result_Class_Value` tells the macro which class in the classifier's output is CD31. The default is `0`, but the correct value depends on the order classes were defined when the classifier was trained. If it is set wrong, the macro produces plausible-looking but incorrect numbers without any error. Verify, against your manual scoring, that the chosen class really corresponds to CD31 before relying on the results.

## Method notes

- The percentage uses `CD31-positive area / ROI area`, **not** ImageJ's `%Area` column.
- The CD31 area comes from the classified result image (the "Create Result" workflow), **not** from the probability/argmax stack.
- `Analyze Particles` runs at `0–Infinity` with no Close, no Fill Holes, and no size filter.

## License

MIT — see [LICENSE](LICENSE).
