cmake_minimum_required(VERSION 3.16)
project(WalnutProject)

# Configure GLFW options before adding it (disable unnecessary features)
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL OFF CACHE BOOL "" FORCE)

# Match Walnut's runtime library setting (dynamic runtime)
set(USE_MSVC_RUNTIME_LIBRARY_DLL ON CACHE BOOL "" FORCE)

# Add vendor dependencies - GLFW first since ImGui depends on it
add_subdirectory(vendor/GLFW)
add_subdirectory(vendor/imgui)

# Add the actual Walnut library
add_subdirectory(Walnut)