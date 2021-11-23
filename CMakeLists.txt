cmake_minimum_required(VERSION 3.5)

project(kaitai_struct_runtime_vendor)

find_package(ament_cmake REQUIRED)

macro(build_ks_cpp_rt)
  set(extra_cmake_args)
  if(DEFINED CMAKE_BUILD_TYPE)
    list(APPEND extra_cmake_args -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})
  endif()
  if(DEFINED CMAKE_TOOLCHAIN_FILE)
    list(APPEND extra_cmake_args "-DCMAKE_TOOLCHAIN_FILE=${CMAKE_TOOLCHAIN_FILE}")
    if(ANDROID)
      if(DEFINED ANDROID_ABI)
        list(APPEND extra_cmake_args "-DANDROID_ABI=${ANDROID_ABI}")
      endif()
      if(DEFINED ANDROID_CPP_FEATURES)
        list(APPEND extra_cmake_args "-DANDROID_CPP_FEATURES=${ANDROID_CPP_FEATURES}")
      endif()
      if(DEFINED ANDROID_FUNCTION_LEVEL_LINKING)
        list(APPEND extra_cmake_args "-DANDROID_FUNCTION_LEVEL_LINKING=${ANDROID_FUNCTION_LEVEL_LINKING}")
      endif()
      if(DEFINED ANDROID_NATIVE_API_LEVEL)
        list(APPEND extra_cmake_args "-DANDROID_NATIVE_API_LEVEL=${ANDROID_NATIVE_API_LEVEL}")
      endif()
      if(DEFINED ANDROID_NDK)
        list(APPEND extra_cmake_args "-DANDROID_NDK=${ANDROID_NDK}")
      endif()
      if(DEFINED ANDROID_STL)
        list(APPEND extra_cmake_args "-DANDROID_STL=${ANDROID_STL}")
      endif()
      if(DEFINED ANDROID_TOOLCHAIN_NAME)
        list(APPEND extra_cmake_args "-DANDROID_TOOLCHAIN_NAME=${ANDROID_TOOLCHAIN_NAME}")
      endif()
    endif()
  else()
    list(APPEND extra_cmake_args "-DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}")
  endif()
  list(APPEND extra_cmake_args "-DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}")

  include(ExternalProject)

  externalproject_add(kaitai_struct_cpp_stl_runtime
    # update URL and URL_HASH when the repo is updated
    URL https://github.com/kaitai-io/kaitai_struct_cpp_stl_runtime/archive/cb09b3a84c7e905c6a3fecfe9617cf864d9af781.zip
    URL_HASH SHA256=058c01828692bf193cb862372651c572fcc43babbf2d664674409511b9f54e5b
    DOWNLOAD_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
      -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install
      ${extra_cmake_args}
  )

  # The external project will install to the build folder, but we'll install that on make install.
  install(
    DIRECTORY
      ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install/
    DESTINATION
      ${CMAKE_INSTALL_PREFIX}
    USE_SOURCE_PERMISSIONS
  )
endmacro()

build_ks_cpp_rt()

ament_export_include_directories(include)
ament_export_libraries(kaitai_struct_cpp_stl_runtime)
# this ensures that the package has an environment hook setting the PATH
ament_package()