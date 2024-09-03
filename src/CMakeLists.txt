# add_subdirectory(core)
# add_subdirectory(editor)
# add_subdirectory(analysis)
# add_subdirectory(visualization)
# add_subdirectory(distributed)
# add_subdirectory(optimization)
# add_subdirectory(api)

# Create the main library
add_library(marl_framework
    main.cpp
)

# Link against dependencies
target_link_libraries(marl_framework
    PRIVATE
        Threads::Threads
        Eigen3::Eigen
        spdlog::spdlog
        nlohmann_json::nlohmann_json
        OpenGL::GL
        glfw
)

if(USE_OPENMP)
    target_link_libraries(marl_framework PRIVATE OpenMP::OpenMP_CXX)
endif()

if(USE_VULKAN)
    target_link_libraries(marl_framework PRIVATE Vulkan::Vulkan)
endif()

# Include directories
target_include_directories(marl_framework
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/../include
)

# Set compile options
target_compile_options(marl_framework
    PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>:/W4 /WX>
        $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -Wpedantic -Werror>
)

# Install rules
install(TARGETS marl_framework
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)