#
# project: fips-recast
#

if (NOT FIPS_IMPORT)
    cmake_minimum_required(VERSION 2.8)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    fips_setup()
    fips_project(fips-recast)
endif()

fips_begin_lib(Detour)
    include_directories(recastnavigation/Detour/Source recastnavigation/Detour/Include)
    fips_dir(recastnavigation/Detour/Include)
    fips_files(DetourAlloc.h DetourAssert.h DetourCommon.h DetourMath.h DetourNavMeshBuilder.h
        DetourNavMesh.h DetourNavMeshQuery.h DetourNode.h DetourStatus.h
    )
    fips_dir(recastnavigation/Detour/Source)
    fips_files(DetourAlloc.cpp DetourCommon.cpp DetourNode.cpp DetourNavMesh.cpp
        DetourNavMeshQuery.cpp DetourNavMeshBuilder.cpp
    )
fips_end_lib()

fips_begin_lib(Recast)
    include_directories(recastnavigation/Recast/Source recastnavigation/Recast/Include)
    fips_dir(recastnavigation/Recast/Include)
    fips_files(Recast.h RecastAlloc.h RecastAssert.h)
    fips_dir(recastnavigation/Recast/Source)
    fips_files(Recast.cpp RecastAlloc.cpp RecastArea.cpp RecastContour.cpp RecastFilter.cpp
        RecastLayers.cpp RecastMesh.cpp RecastMeshDetail.cpp RecastRasterization.cpp
        RecastRegion.cpp
    )
fips_end_lib()

fips_begin_lib(DetourCrowd)
    include_directories(
        recastnavigation/DetourCrowd/Source
        recastnavigation/DetourCrowd/Include
        recastnavigation/Detour/Include
        recastnavigation/Recast/Include
    )
    fips_dir(recastnavigation/DetourCrowd/Include)
    fips_files(DetourCrowd.h DetourLocalBoundary.h DetourObstacleAvoidance.h
        DetourPathCorridor.h DetourPathQueue.h DetourProximityGrid.h
    )
    fips_dir(recastnavigation/DetourCrowd/Source)
    fips_files(DetourCrowd.cpp DetourLocalBoundary.cpp DetourObstacleAvoidance.cpp
        DetourPathCorridor.cpp DetourPathQueue.cpp DetourProximityGrid.cpp
    )
fips_end_lib()

fips_begin_lib(DetourTileCache)
    include_directories(
        recastnavigation/DetourTileCache/Source
        recastnavigation/DetourTileCache/Include
        recastnavigation/Detour/Include
        recastnavigation/Recast/Include
    )
    fips_dir(recastnavigation/DetourTileCache/Include)
    fips_files(DetourTileCacheBuilder.h DetourTileCache.h)
    fips_dir(recastnavigation/DetourTileCache/Source)
    fips_files(DetourTileCache.cpp DetourTileCacheBuilder.cpp)
fips_end_lib()

fips_begin_lib(DebugUtils)
    include_directories(
        recastnavigation/DebugUtils/Source
        recastnavigation/DebugUtils/Include
        recastnavigation/Detour/Include
        recastnavigation/DetourTileCache/Include
        recastnavigation/Recast/Include
    )
    fips_dir(recastnavigation/DebugUtils/Include)
    fips_files(DebugDraw.h DetourDebugDraw.h RecastDebugDraw.h RecastDump.h)
    fips_dir(recastnavigation/DebugUtils/Source)
    fips_files(DebugDraw.cpp DetourDebugDraw.cpp RecastDebugDraw.cpp RecastDump.cpp)
fips_end_lib()

if (FIPS_MACOS OR FIPS_LINUX OR FIPS_WINDOWS)
    fips_begin_app(RecastDemo cmdline)
        if (FIPS_WINDOWS)
            include_directories(include)
        endif()
        add_definitions(-DGLFW_INCLUDE_GLEXT)
        include_directories(
            recastnavigation/RecastDemo/Source
            recastnavigation/RecastDemo/Include
            recastnavigation/RecastDemo/Contrib
            recastnavigation/RecastDemo/Contrib/fastlz
            recastnavigation/DebugUtils/Include
            recastnavigation/Detour/Include
            recastnavigation/DetourCrowd/Include
            recastnavigation/DetourTileCache/Include
            recastnavigation/Recast/Include
        )
        fips_dir(recastnavigation/RecastDemo/Include)
        fips_files(ChunkyTriMesh.h ConvexVolumeTool.h CrowdTool.h Filelist.h imgui.h
            imguiRenderGL.h InputGeom.h MeshLoaderObj.h NavMeshPruneTool.h NavMeshTesterTool.h
            OffMeshConnectionTool.h PerfTimer.h Sample_Debug.h Sample.h SampleInterfaces.h
            Sample_SoloMesh.h Sample_TempObstacles.h Sample_TileMesh.h SlideShow.h
            TestCase.h ValueHistory.h
        )
        fips_dir(recastnavigation/RecastDemo/Source)
        fips_files(ChunkyTriMesh.cpp ConvexVolumeTool.cpp CrowdTool.cpp Filelist.cpp imgui.cpp
            imguiRenderGL.cpp InputGeom.cpp MeshLoaderObj.cpp NavMeshPruneTool.cpp
            NavMeshTesterTool.cpp OffMeshConnectionTool.cpp PerfTimer.cpp Sample_Debug.cpp
            Sample.cpp SampleInterfaces.cpp Sample_SoloMesh.cpp Sample_TempObstacles.cpp
            Sample_TileMesh.cpp SlideShow.cpp TestCase.cpp ValueHistory.cpp main.cpp
        )

        # use fipsified stb package instead of this
        fips_dir(recastnavigation/RecastDemo/Contrib)
        fips_files(stb_image.h stb_truetype.h)

        # add a fipsified fastlz lib instead of this
        fips_dir(recastnavigation/RecastDemo/Contrib/fastlz)
        fips_files(fastlz.c fastlz.h)

        fips_deps(DebugUtils Detour DetourCrowd DetourTileCache Recast glfw3)

        if (FIPS_MACOS)
            fips_dir(recastnavigation/RecastDemo/Include)
            fips_dir(recastnavigation/RecastDemo/Source)
        endif()

        if (FIPS_WINDOWS)
            fips_libs(opengl32)
        endif()

        if (FIPS_LINUX)
            fips_libs(X11 Xrandr Xi Xinerama Xxf86vm Xcursor GL)
        endif()

    fips_end_app()
    if (FIPS_MACOS OR FIPS_LINUX)
        set_target_properties(RecastDemo PROPERTIES COMPILE_FLAGS "-Wunused-value -Wreorder -Wsign-compare -Wall -Wno-missing-field-initializers")
    endif()
endif()

if (NOT FIPS_IMPORT)
    fips_finish()
endif()

