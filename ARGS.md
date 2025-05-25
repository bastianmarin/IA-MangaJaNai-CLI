# Documentation of Arguments and Allowed Types

This document describes all the types of arguments accepted by the MangaJaNai-CLI code, both from the command line (CLI) and from the advanced configuration file (JSON). It includes what is allowed, what is not, examples, and validations.

---

## 1. Command Line Arguments (CLI)

The main script is `run_upscale.py`. The main arguments are:

### Usage Example:
```bash
python run_upscale.py -f "path/to/file.jpg" -u 4
python run_upscale.py -d "path/to/folder/" -o "output/path/"
python run_upscale.py --settings "path/to/appstate2.json"
```

### Available Arguments:
- `--settings <file.json>`: Uses an advanced configuration file (see section 2).
- `-f, --file-path <file>`: Absolute path to an image file to process.
- `-d, --folder-path <folder>`: Absolute path to a folder for batch processing.
- `-o, --output-folder-path <folder>`: Output folder. Default: `./out`
- `-m, --models-directory-path <folder>`: Folder with models. Default: `../models`
- `-u, --upscale-factor <1|2|3|4>`: Upscale factor. Default: 2
- `--device-index <int>`: GPU index to use. Default: 0

#### Allowed Types:
- Paths: absolute or relative string
- Numbers: integers (`int`)
- Factors: only 1, 2, 3, 4
- Booleans: true/false (in JSON or config)

#### Not Allowed:
- Factors outside 1-4
- Negative GPU indices
- Empty or non-existent paths/files

---

## 2. Advanced Configuration File Arguments (JSON)

The advanced file (example: `default_cli_configuration.json`) allows you to control all parameters. Important keys:

### Root
- `SelectedDeviceIndex` (int): GPU index
- `UseCpu` (bool): Force CPU usage
- `UseFp16` (bool): Use FP16
- `ModelsDirectory` (str): Models folder
- `Workflows` (array): List of workflows

### Inside `Workflows > $values > [0]`
- `SelectedTabIndex` (int): 0 = file, 1 = folder
- `InputFilePath` (str): File path (if TabIndex=0)
- `InputFolderPath` (str): Folder path (if TabIndex=1)
- `OutputFilename` (str): Output name, use `%filename%` to keep the same name
- `OutputFolderPath` (str): Output folder
- `OverwriteExistingFiles` (bool): Overwrite files
- `UpscaleImages` (bool): Process images
- `UpscaleArchives` (bool): Process compressed files
- `WebpSelected`, `AvifSelected`, `PngSelected`, `JpegSelected` (bool): Only one should be true
- `UpscaleScaleFactor` (int): 1, 2, 3, 4
- `LossyCompressionQuality` (int): 0-100
- `UseLosslessCompression` (bool)
- `Chains > $values` (array): Advanced configuration of models and conditions

#### Example JSON fragment:
```json
{
  "SelectedDeviceIndex": 0,
  "UseCpu": false,
  "UseFp16": true,
  "ModelsDirectory": "../models",
  "Workflows": {
    "$values": [
      {
        "SelectedTabIndex": 0,
        "InputFilePath": "C:/img.jpg",
        "OutputFolderPath": "C:/out/",
        "UpscaleImages": true,
        "WebpSelected": true,
        "UpscaleScaleFactor": 2,
        ...
      }
    ]
  }
}
```

#### Allowed Types:
- Strings, integers, booleans, arrays of objects
- Paths must be valid and exist
- Factors and options must be within the indicated ranges

#### Not Allowed:
- Out-of-range values (e.g. `UpscaleScaleFactor: 5`)
- More than one output format set to true
- Empty strings in required paths

---

## 3. Validations and Type Examples

- **int**: 0, 1, 2, 3, 4, ...
- **bool**: true, false
- **str**: "C:/path/file.jpg"
- **array**: [obj1, obj2, ...]
- **enum**: only allowed values (e.g. image formats)

### Error Example:
- If you pass `UpscaleScaleFactor: 5` → Error: only 1-4 allowed
- If you pass a non-existent model path → Error: file not found

---

## 4. Internal Arguments and Code Validations

- Validation methods (`SettingsParser`) throw an error if the type is incorrect.
- Inputs and outputs use strict types (`int`, `str`, `bool`, etc.)
- Optional values must be explicit (`null` or not present)

---

## 5. Summary of Not Allowed Arguments

- Incorrect types (e.g. string where int is expected)
- Out-of-range values
- More than one output format selected
- Empty or invalid paths
- Negative GPU indices

---

## 6. Resources
- See `README.md` for usage examples
- See `default_cli_configuration.json` for advanced template
- Run `python run_upscale.py -h` for CLI help

---

*Last update: May 2025*
