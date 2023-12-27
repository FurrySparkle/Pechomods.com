---
title: The Basics
date: 2023-12-10 00:00:00 +0000
categories: [General Information]
tags: [essential, batch]
pin: true
description: The basics of modding for Into the Radius. Covers extracting game files, the game's file structure, file formats, cooking files, and creating .pak files.
author: "Stuchoo"
---

## Important Information

### Required Software
-  Unreal Engine 4.27 [(Link to download)](https://www.unrealengine.com/en-US/download)
- FModel [(Link to github)](https://github.com/4sval/FModel/releases/tag/4.2.1.0)
- UAssetGUI [(Link to github)](https://github.com/atenfyr/UAssetGUI)
### Understanding the File Structure  
When creating a project in Unreal Engine 4, the project will be saved to a directory. Within that directory will be the project. For example: `<SaveDirectory>/<ProjectName>/…`. `<ProjectName>` houses all of the contents of the Unreal Engine 4 project. In that project folder, there will be a subfolder named `Content`. That folder houses all of the assets and blueprints within a game.

In the case of Into the Radius, the project name is `IntoTheRadius`. Within that folder, there is a folder called `Content`. Again, this houses everything inside a game except for configuration files. However, note that when you open the game files in UModel, there may not be a `Content` folder. Instead, it’ll have been replaced with a `Game` folder. 

The majority of the modding that you will do will be under the `Content` folder. If a file that you are looking to override is in `.../IntoTheRadius/Game/ITR/Configurations/Infos/DT_ItemInfos`, the file path to copy would be `.../Content/ITR/Configurations/Infos/DT_ItemInfos`.

> Failure to copy the exact path of Into The Radius will result in a broken mod.
{: .prompt-danger }

### File Formats  
There are multiple different file formats that you may encounter while modding. Here is a list of most of them, and what each of them are.
- **.psk** - A skeletal or static mesh. May contain the skeleton.
- **.psa** - An animation for a specific mesh.
- **.uasset** - Two types.
  1. Raw .uasset files are the ones created after cooking a UE4 project. 
  2. UE4 .uasset files are the ones that are taken directly from a UE4 project, they are uncooked and can be transferred between projects.
- **.uexp** - An raw complementary file to uassets, usually containing import and export maps.
- **.ubulk** - Another data storage file that UE4 uses. 
- **.umap** - A level/map file, similar to .uasset in the way that there is an uncooked and cooked version.

  
  
## Method 1: Extract and Repack
This method works best for simple tweak mods. 

### Extracting Game Files  
There are two methods of extracting games files, both have important differences:
1. [FModel](https://unofficial-modding-guide.com/posts/tools). Fmodel is an advanced browser for Pak files. Upon opening Fmodel, you will see something like the screenshot below. Open the 

Each entry on the left menu is for one .pak file. pakchunk0-windowsNoEditor.pak is the native game. Any others you see here are mods. Fmodel can be used to look at and export both native and modded files. Double click on `pakchunk0-windowsNoEditor.pak` and click on the folders tab. Here you can navigate to whatever content it is you want to change. For example, if you wanted to change what enemy spawns at the start of *Pervomay Route*, you would navigate to `IntoTheRadius\Content\ITR\Levels\Zone\Buffer`. 
Once here, click on the Packages tab and double click on `L_Zone_Buffer_Monsters.umap`. Now in the right pane, you can read through everything in the .umap file but you can’t edit it. To make any changes you have to export it. Right click on `L_Zone_Buffer_Monsters.umap` and click Export Raw Data.



2. [UModel](https://unofficial-modding-guide.com/posts/tools). This program can extract non-raw files, allowing you to gain access to textures, models, animations, and sounds. To use it, direct it towards the location of the .pak files within Into the Radius: `<GameInstallLocation>\IntoTheRadius\IntoTheRadius\Content\Paks`. You'll be able to extract individual assets once loaded. Note the you will need to cook assets you extract using UModel. 

### Simple File Modifications
For small/simple mods, UAssetGUI is the easiest way to make changes to files. Just be aware that mods created in this way will be completely incompatible with other mods which modify the same file. To start, open UAssetGUI and click file>open. As an example, you can open L_Zone_Buffer_Monsters.umap from where it was exported by FModel earlier. Click on the boxes to expand nodes. Here we will navigate to BP_MonsterSpawner2 (pervomay 1st mission house) which you can see is currently a spawn location for a fragment.

If you simply wanted to change the spawn to something else you could double click the box where it says NPC.Monster.Fragment and replace it with the enemy you want. However, for this example, we want to make it a 10% chance a fragment spawns and a 90% chance a mimic policemen spawns. To do this, we need to add a new row to the first Monsters node. Click the Monsters(1) node and click any row in the table. Once it is highlighted, copy and paste it to add a new row.



Now to get UAssetGUI to register the new row, you need to click View>Recalculate Nodes. Navigate to the node you were editing and you should now see two Monsters nodes under the main Monsters node. One is for the original and one is for the new row you created. At this point, you should also save. Now we just need to fill in the new node. In the value column row 1, enter a value of 9. Then fill out the key node to look the same as the original node, but with `NPC.Monster.Mimic.PM` in place of `NPC.Monster.Fragment`. 


### Creating a PAK File  
There are two main ways of packing mods for into the radius.
#### 1. **UnrealPakGUI**
	To set it up, make sure Engine Dir points to your unreal engine installation and make sure UProjectFile points to the intoTheRadius.UProject we exported in Fmodel earlier. Drag and drop the .umap and .uexp files we modified in UAssetGUI into the UnrealPakGUI window. Also make sure you deselect the encryption option in the top right. Once everything if configured as shown below you can click create pak. **IMPORTANT: MAKE SURE YOU HAVE _P AT THE END OF YOUR FILENAME, AND MAKE SURE THERE ARE NO SPACES IN THE FILE PATH WHERE YOU SAVE THE .PAK**



#### 2. **ITR_Packer**
	Unfortunately, UnrealPakGUI refuses to read new tag files which you may use for custom items. This is where a somewhat complicated setup shown to me by Saedriss comes in.
	Create a new folder for your pak file. Make sure there are no spaces in the file path. Now create a blank notepad file called ITRPacker.txt (or something else).

Open it up and add the following lines:

```batch
"PATH_TO_YOUR_UNREAL_ENGINE_INSTALLATION" PATH_TO_FOLDER_YOU_CREATED_FOR_THE_PACKER\NAME_FOR_YOUR_PAK_FILE.pak -create=PATH_TO_FOLDER_YOU_CREATED_FOR_THE_PACKER\to_pak_ITR_packer.txt -patchpaddingalign=2048 -multiprocess

pause 
```
Now save the notepad file, and rename it from a .txt file to a .bat file.



Next, create a blank text file called “to_pak_ITR_packer.txt”. Open it in notepad. Add the following lines. If you aren't adding any custom tags you can ignore that line. 
```batch
    "PATH_TO_YOUR_MODDED_TAG_FILE\YOUR_TAG_FILE.ini" "../../../IntoTheRadius/Config/Tags/YOUR_TAG_FILE.ini"
    
    "PATH_TO_YOUR_INTO_THE_RADIUS_CONTENT_FOLDER" "../../../IntoTheRadius/Content/"
```






## Method 2: Using ChuckSharts's ITR-SDK
Thanks to ChuckSharts working through the process of uncooking much of the game and building and SDK, we can now do a significant portion of modding using unreal engine directly. Just [download the SDK](https://drive.google.com/file/d/1yPFEPYuxcBEV3NKYOZnAxRIyEIQFFygn/view) and make your modifications. Once you've made your modifications, just cook your assets, and package the specific assets you've modified. 
### Simple Example
For this example, we will make the Makarov PM pistol one shot every enemy in the game. 
#### DataTable Changes
First download the SDK and open it up with unreal engine 4.27. Navigate to `Content\Mods\ModdedData` in the content browser. Create a new DataTable and set the row structure to WeaponInfo. Give the dataTable a name (I called mine DT_OP_Makarov_WeaponInfos). Now navigate to `Content\ITR\Configurations\Infos` and open DT_weaponInfos. In DT_weaponInfos, find the row for the PM and copy it. Then open the new dataTable you created and paste the row for the PM into it. Now click on the row for the PM, and find the entry for ammo damage. Change the value from 15 to 500. Save the dataTable. 


Now we need to add our dataTable to the list the game uses. In the SDK, we use Composite data tables to make mod compatibility as easy as possible. Navigate to `Content\Mods\ModdedData\CompositeDataTables` and open DTC_WeaponInfos. In parent tables, add a new element and set the new blank entry to your new dataTable. 


#### Cooking
Because we're working from unreal engine, the files need to be cooked before they can can be packaged. First go to `file\Package Project\Packaging Settings`. Navigate down to "Directories Never to Cook" (it may be hidden under a folder). Here we want to add any folders which we aren't using. So because we aren't doing any ART modifications, the folders ART, ART2, and ITR_art can be added. 


Now cook the project by clicking `file\PackageProject\Windows(64bit)`. If you have any errors (shown in red on the console), navigate to the asset it points to and either delete the asset or delete whatever inside the asset is causing the error (or fix it). For example, when I downloaded the SDK, I had to go to `Content\ITR\BPs\Items\Weapons\Primary\AKM\BP_Mag_AKM.uasset` and remove the scene10 node from the event graph in the BP. 

Now you can finish the mod by using one of the packing processes outlined earlier in this article. For this mod the specific files you need are
 "DT_You_Created".uasset (located at `WindowsNoEditor\IntoTheRadius\Content\Mods\ModdedData`), DTC_WeaponInfos.uasset (`WindowsNoEditor\IntoTheRadius\Content\Mods\ModdedData\CompositeDataTables`), and DA_InfosConfig.uasset (`WindowsNoEditor\IntoTheRadius\Content\ITR\Configurations`). Make sure your file structure when packing is the same as the file structure in unreal engine. Also make sure you only pack the files you need. 

You should now be able to install the mod and have an OP makarov in game. 

## Additional Information

### Mod Installation  
All .pak mods go here. `<GameInstallLocation>\IntoTheRadius\IntoTheRadius\Content\Paks`. Ensure that the .pak load order is correct.


### Debugging  
If your mod is not working, make sure to check these common error points:
- Check the file strucuture. This must perfectly match the files within the game, otherwise it will not load.
- There must be a `_P` at the end of the mod name. This is needed when a patch pak is used for the main game files.
- Load order. Incorrecy load order may cause issues between mods, change the numberical elements of the pak name to change the load order.
- Mount point (The best way to debug any issues is to use the builtin UnrealPak.exe. Simply open this with cmd with the line: `unreakpak.exe -List This_Is_A_Pak.pak`. Look at the mounting point. The mounting point will be the deepest folder that encapulates all of your content. If it is not, you may have an issue.)
