if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
    # Settings when 'examples' is the root projet
    cmake_minimum_required(VERSION 3.20)
    project(zenohcxx_examples LANGUAGES C CXX)
    include(../cmake/helpers.cmake)
    set_default_build_type(Release)
    configure_include_project(ZENOHCXX_ZENOHC zenohc zenohc::lib "../../zenoh-c" zenohc "https://github.com/eclipse-zenoh/zenoh-c" "")
    configure_include_project(ZENOHPICO zenohpico zenohpico "../../zenoh-pico" zenohc "https://github.com/eclipse-zenoh/zenoh-pico" "")
    configure_include_project(ZENOHCXX zenohcxx zenohcxx ".." zenohcxx "https://github.com/eclipse-zenoh/zenoh-cpp" "")
    add_custom_target(examples ALL)
else()
    message(STATUS "zenoh-cxx examples")
    add_custom_target(examples)
endif()

function(add_example file mode lib)
	get_filename_component(filename ${file} NAME_WE)
	set(target ${filename}_${mode})
	add_executable(${target} EXCLUDE_FROM_ALL ${file})
	set_target_properties(${target} PROPERTIES 
		OUTPUT_NAME ${filename}
		RUNTIME_OUTPUT_DIRECTORY ${mode})
	add_dependencies(examples ${target})
	add_dependencies(${target} ${lib})
    target_link_libraries(${target} PUBLIC ${lib})
	set_property(TARGET ${target} PROPERTY LANGUAGE CXX)
	set_property(TARGET ${target} PROPERTY CXX_STANDARD 17)
	add_platform_libraries(${target})
endfunction()

function(add_examples glob mode lib)
	file(GLOB files ${glob})
	foreach(file ${files})
		add_example(${file} ${mode} ${lib})
	endforeach()
endfunction()

# zenohcxx examples compiled with C++ compiler with zenohc
add_examples("${CMAKE_CURRENT_SOURCE_DIR}/zenohc/*.cxx" zenohc zenohcxx::zenohc::lib)
add_examples("${CMAKE_CURRENT_SOURCE_DIR}/universal/*.cxx" zenohc zenohcxx::zenohc::lib)

# zenohcxx examples compiled with C++ compiler with zenohpico
add_examples("${CMAKE_CURRENT_SOURCE_DIR}/universal/*.cxx" zenohpico zenohcxx::zenohpico)

# check compilation of simple examples
add_examples("${CMAKE_CURRENT_SOURCE_DIR}/simple/universal/z_simple.cxx" zenohpico zenohcxx::zenohpico)
add_examples("${CMAKE_CURRENT_SOURCE_DIR}/simple/universal/z_simple.cxx" zenohc zenohcxx::zenohc::lib)
add_examples("${CMAKE_CURRENT_SOURCE_DIR}/simple/zenohpico/zp_simple.cxx" zenohpico zenohcxx::zenohpico)
add_examples("${CMAKE_CURRENT_SOURCE_DIR}/simple/zenohc/zc_simple.cxx" zenohc zenohcxx::zenohc::lib)