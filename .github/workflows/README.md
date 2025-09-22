# ONNX Runtime Build Workflows

This directory contains GitHub Actions workflows for building ONNX Runtime with custom configurations.

## Available Workflows

### 1. `build-onnxruntime.yml` - Manual Build with Options

**Trigger:** Manual dispatch only (`workflow_dispatch`)

**Features:**
- Choose Python version (3.9, 3.10, 3.11, 3.12, 3.13)
- Choose build configuration (Debug, Release, RelWithDebInfo)
- Toggle DEBUG_NODE_INPUTS_OUTPUTS flag
- Uploads wheel as artifact
- Tests wheel installation

**How to use:**
1. Go to the "Actions" tab in your GitHub repository
2. Select "Build ONNX Runtime" workflow
3. Click "Run workflow"
4. Choose your desired options
5. Click "Run workflow"

### 2. `build-latest-python.yml` - Automated Multi-Python Build

**Trigger:**
- Push to main/master branches
- Pull requests to main/master branches
- Manual dispatch

**Features:**
- Builds for Python 3.11, 3.12, and 3.13
- Always enables DEBUG_NODE_INPUTS_OUTPUTS
- Uses RelWithDebInfo configuration
- Verifies Python version matches wheel tag
- Uploads separate artifacts for each Python version

## Build Configuration

Both workflows use the same build command that you used locally:

```bash
./build.sh --config RelWithDebInfo --build --build_wheel --parallel --skip_tests --cmake_extra_defines onnxruntime_DEBUG_NODE_INPUTS_OUTPUTS=1
```

## Key Differences from Local Build

1. **Python Version Control**: The workflows explicitly set the Python version using `actions/setup-python@v4`, ensuring the wheel is built for the correct Python version.

2. **Clean Environment**: Each workflow starts with a fresh macOS runner, avoiding any local environment issues.

3. **System Dependencies**: Automatically installs required system dependencies (CMake, protobuf, etc.).

4. **Verification**: Includes steps to verify the wheel was built correctly and test basic functionality.

## Artifacts

After a successful build, you can download the wheel from the "Artifacts" section of the workflow run:

- `onnxruntime-wheel-python3.13` - Wheel built for Python 3.13
- `onnxruntime-wheel-python3.12` - Wheel built for Python 3.12
- `onnxruntime-wheel-python3.11` - Wheel built for Python 3.11

## Troubleshooting

### Common Issues

1. **Build fails with "could not load cache"**: This is handled by the clean build process in the workflows.

2. **Python version mismatch**: The workflows explicitly verify that the wheel Python version matches the expected version.

3. **Missing dependencies**: The workflows install all required system and Python dependencies.

### Local Testing

To test the build process locally with the same Python version as the workflow:

```bash
# Use the same Python version as the workflow
python3.13 -m venv test_env
source test_env/bin/activate

# Install dependencies
pip install --upgrade pip setuptools wheel numpy pybind11

# Run the build
./build.sh --config RelWithDebInfo --update --cmake_extra_defines onnxruntime_DEBUG_NODE_INPUTS_OUTPUTS=1
./build.sh --config RelWithDebInfo --build --build_wheel --parallel --skip_tests --cmake_extra_defines onnxruntime_DEBUG_NODE_INPUTS_OUTPUTS=1
```

## Customization

You can modify the workflows to:

- Add more Python versions to the matrix
- Change build configurations
- Add additional CMake defines
- Modify the artifact retention period
- Add additional testing steps

## Notes

- The workflows are configured for macOS (Apple Silicon) builds
- For other platforms, you would need to modify the `runs-on` field and system dependencies
- The workflows assume the repository has the standard ONNX Runtime structure
