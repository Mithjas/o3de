#
# Copyright (c) Contributors to the Open 3D Engine Project.
# For complete copyright and license terms please see the LICENSE at the root of this distribution.
#
# SPDX-License-Identifier: Apache-2.0 OR MIT
#
#

ly_add_target(
    NAME GradientSignal.Static STATIC
    NAMESPACE Gem
    FILES_CMAKE
        gradientsignal_files.cmake
    INCLUDE_DIRECTORIES
        PRIVATE
            Source
        PUBLIC
            Include
    BUILD_DEPENDENCIES
        PUBLIC
            AZ::AzCore
            AZ::AtomCore
            AZ::AzFramework
            Gem::SurfaceData
            Gem::ImageProcessingAtom.Headers
            Gem::LmbrCentral
)

ly_add_target(
    NAME GradientSignal ${PAL_TRAIT_MONOLITHIC_DRIVEN_MODULE_TYPE}
    NAMESPACE Gem
    FILES_CMAKE
        gradientsignal_shared_files.cmake
    INCLUDE_DIRECTORIES
        PRIVATE
            Source
        PUBLIC
            Include
    BUILD_DEPENDENCIES
        PRIVATE
            Gem::LmbrCentral
        PUBLIC
            AZ::AzCore
            AZ::AtomCore
            Gem::GradientSignal.Static
            Gem::ImageProcessingAtom.Headers # Atom/ImageProcessing/PixelFormats.h is part of a header in Includes
    RUNTIME_DEPENDENCIES
        Gem::LmbrCentral
)

# Load the "Gem::GradientSignal" module in Clients and Servers
ly_create_alias(NAME GradientSignal.Clients NAMESPACE Gem TARGETS Gem::GradientSignal)
ly_create_alias(NAME GradientSignal.Servers NAMESPACE Gem TARGETS Gem::GradientSignal)

if(PAL_TRAIT_BUILD_HOST_TOOLS)
    
    ly_add_target(
        NAME GradientSignal.Editor.Static STATIC
        NAMESPACE Gem
        FILES_CMAKE
            gradientsignal_editor_files.cmake
        INCLUDE_DIRECTORIES
            PRIVATE
                Source
            PUBLIC
                Include
        COMPILE_DEFINITIONS
            PUBLIC
                GRADIENTSIGNAL_EDITOR
        BUILD_DEPENDENCIES
            PRIVATE
                Gem::LmbrCentral.Editor
            PUBLIC
                3rdParty::Qt::Widgets
                AZ::AzCore
                AZ::AtomCore
                AZ::AzFramework
                AZ::AzToolsFramework
                AZ::AssetBuilderSDK
                Gem::GradientSignal.Static
                Gem::SurfaceData
        RUNTIME_DEPENDENCIES
            Gem::LmbrCentral.Editor
    )

    ly_add_target(
        NAME GradientSignal.Editor GEM_MODULE
        NAMESPACE Gem
        FILES_CMAKE
            gradientsignal_editor_shared_files.cmake
        INCLUDE_DIRECTORIES
            PRIVATE
                Source
            PUBLIC
                Include
        BUILD_DEPENDENCIES
            PRIVATE
                Gem::GradientSignal.Editor.Static
                Gem::LmbrCentral.Editor
            PUBLIC
                AZ::AtomCore
        RUNTIME_DEPENDENCIES
            Gem::LmbrCentral.Editor
    )

    # Load the "Gem::GradientSignal.Editor" module in Builders and Tools
    ly_create_alias(NAME GradientSignal.Builders NAMESPACE Gem TARGETS Gem::GradientSignal.Editor)
    ly_create_alias(NAME GradientSignal.Tools    NAMESPACE Gem TARGETS Gem::GradientSignal.Editor)


endif()

################################################################################
# Tests
################################################################################
if(PAL_TRAIT_BUILD_TESTS_SUPPORTED)
    ly_add_target(
        NAME GradientSignal.Mocks HEADERONLY
        NAMESPACE Gem
        FILES_CMAKE
            gradientsignal_mocks_files.cmake
        INCLUDE_DIRECTORIES
            INTERFACE
                Mocks
    )
    
    ly_add_target(
        NAME GradientSignal.Tests ${PAL_TRAIT_TEST_TARGET_TYPE}
        NAMESPACE Gem
        FILES_CMAKE
            gradientsignal_tests_files.cmake
        INCLUDE_DIRECTORIES
            PRIVATE
                .
                Source
        BUILD_DEPENDENCIES
            PRIVATE
                AZ::AzTest
                Gem::GradientSignal.Static
                Gem::LmbrCentral
                Gem::GradientSignal.Mocks
    )
    ly_add_googletest(
        NAME Gem::GradientSignal.Tests
    )

    if(PAL_TRAIT_BUILD_HOST_TOOLS)
        ly_add_target(
            NAME GradientSignal.Editor.Tests ${PAL_TRAIT_TEST_TARGET_TYPE}
            NAMESPACE Gem
            FILES_CMAKE
                gradientsignal_editor_tests_files.cmake
            INCLUDE_DIRECTORIES
                PRIVATE
                    .
                    Source
            BUILD_DEPENDENCIES
                PRIVATE
                    AZ::AzTest
                    Gem::GradientSignal.Static
                    Gem::GradientSignal.Editor.Static
                    Gem::LmbrCentral.Editor
        )
        ly_add_googletest(
            NAME Gem::GradientSignal.Editor.Tests
        )
    endif()

endif()
