# Algo-reproduce

This repository documents all open-source algorithms that I have reproduced up to this point, as well as the modifications I have made.

Due to there are many algorithms involved, I have placed the more effective algorithms and tool-oriented algorithms at the forefront, with special annotations provided. Because of my limited time and capabilities, I am unable to conduct exhaustive tests on all algorithms, so there may be some shortcomings. Feedback is welcome💡.

⭐ If you find this repository useful to your research or work, it is really appreciated to star this repository.

✉️ Any additions or suggestions, feel free to contribute and contact [tangruiqing123@gmail.com](tangruiqing123@gmail.com).

![](./assets/programmer.png)

# Table of Contents

## [Tools](#tools)

## [text to 3d human motion](#myanchor1)

## [music to 3d human dance](#myanchor2)

## [3d human motion capture](#myanchor3)

## [other](#myanchor4)

Currently, I have integrated text2motion and music2dance into my 🎮Discord community. Due to the unstable server magic and algorithm inefficiency, it is temporarily unavailable for public access. I will continue to update and expect to go live by the end of October.

|               Task                |    Status     |                           Roadmap                            |
| :-------------------------------: | :-----------: | :----------------------------------------------------------: |
|            Text2Motion            |  ⛳supported   | Multi-role Selection<br>Customize Roles<br />vmd&fbx Download Link<br />Fix Physical Effects |
|            Music2Dance            |  ⛳supported   | Multi-role Selection<br/>Customize Roles<br />vmd&fbx Download Link<br />Fix Physical Effects |
|     Singing Voice Conversion      | 👨‍💻 developing |                            · · ·                             |
| LLM Role Play (Voice & Text Chat) | 👨‍💻 developing |                            · · ·                             |
|          Motion Capture           | 👨‍💻 developing |                            · · ·                             |

Here are some result demonstrations.

![](./assets/discord.png)

## ⭐You can find all the results in this [directory](https://github.com/RuiqingTang/Algo-reproduce/tree/main/assets).

## Tools{#tools}

1. smpl2fbx

   https://github.com/softcat477/SMPL-to-FBX
   https://www.winehq.org/
   Thanks to the authors for releasing the code. I can convert the SMPL pose parameters into FBX. If you are not familiar with SMPL, you can refer to [the official website](https://smpl.is.tue.mpg.de/). If you wish to delve deeper, you can also visit this [website](https://smpl-x.is.tue.mpg.de/). However, there is an issue with the fbx I converted; its initial position is not at the origin of the three-dimensional coordinate system.

   ![](./assets/fbx_issue.png)

   I addressed this issue through some post-processing work. I executed the following command in Blender:

   ```python
   import bpy
   obj = bpy.context.object
   obj.location = (0, 0, -0.3)
   obj.rotation_euler = (3.1, 0, 0)
   ```

   The final result is as follows:

   ![](./assets/fix_fbx_issue.png)

2. smpl2vmd

   https://github.com/rongakowang/MMDMC

   Thanks to the authors for releasing the code. I can convert the SMPL pose parameters into VMD. To be precise, the model used is SMPL+H. SMPL+H includes additional hand information compared to SMPL. I set all these additional hand information to 0, and the program still runs correctly.

   Thanks to the [developer](https://github.com/UuuNyaa) of mmd_tools（[official website](https://mmd-blender.fandom.com/wiki/MMD_Tools) | [github pages]( https://github.com/UuuNyaa/blender_mmd_tools)). There are still some issues with *IK Toggle*, and I look forward to your subsequent fixes.

3. fbx2vmd

   I'm very sorry, I can no longer find the repository of the author. I found this [repository](https://github.com/tomtomtong/FBXtoVMD), but I haven't verified it yet.

   Through this project, combined with [Unity 2020.3.48f1](https://unity.com/cn/releases/editor/whats-new/2020.3.48#installs), I can convert fbx to vmd. Whether it's an fbx converted from smpl or an fbx downloaded from mixamo, it can all be converted using this tool. However, this conversion is lossy and often results in abnormal movements. Additionally, not all FBX files are supported, leading to issues with bone binding errors.

## <span id='myanchor1'>text to 3d human motion</span>



1. stmc✨

   :crystal_ball: Code: https://github.com/nv-tlabs/stmc

   :volleyball: Projects Page: https://mathis.petrovich.fr/stmc/

   📚Paper: https://arxiv.org/abs/2401.08559

   📝 Note: In fact, STMC is the algorithm that I consider to be the most effective at present. However, due to its stringent requirements for prompts (must include descriptions of the actions, the body parts involved, the duration of the actions, they are separated by "|". ) , from an engineering perspective, I had to abandon it, or integrate a LLM to modify the prompts to meet the requirements. In my tests (Prompts are generated by GPT-4) , I disregarded the stringent requirements for prompts, ensuring only that their format was correct. This results in its performance not being as optimal as desired.

   📀Results: You can check the demo result and my test results [here](https://github.com/RuiqingTang/Algo-reproduce/tree/main/assets/text2motion/stmc).

2. FlowMDM✨

   :crystal_ball: Code: https://github.com/BarqueroGerman/FlowMDM

   :volleyball: Projects Page: https://barquerogerman.github.io/FlowMDM/

   📚Paper: https://arxiv.org/abs/2402.15509

   📝 Note: Personally, I find this algorithm to be excellent. I have decided to integrate it into my Discord community by September 30, 2024. I have made some modifications to this algorithm. 

   Firstly, I need to address the input format. In FlowMDM, the input data is a JSON file containing two fields: lengths and text. 

   eg:

   ```json
   {
       "lengths": [99,99],
       "text": ["backflip","punch in the air"]
       }
   ```

   I believe this can be integrated with a LLM to process user inputs, but I ultimately decided to design a smaller model (encoder) myself as the input to the FlowMDM model. Of course, I may need to retrain the model, but I believe the time spent is worthwhile.

   Secondly, considering the issue of algorithm efficiency, it currently takes a very long time. In the example above, it takes 43 seconds to export the FBX. This forces me to find ways to improve it. Since FlowMDM is based on MDM, and MDM recently open-sourced a diffusion model that only requires 50 steps, compared to the current 1000 steps, which can significantly reduce the time consumption. I will use this as a point of improvement. 

   Additionally, the model outputs JOINT poses, which need to be converted into SMPL. Fortunately, previous researchers have provided a *joint2smpl* script. However, this step is actually very time-consuming. I examined its source code and found that the issue lies with the optimizer. The author used the *LBFGS* optimizer, which incurs significant computational overhead. I replaced it with *Adam*, which, although it does not achieve the same final performance as *LBFGS* (prone to Z-fighting issues), is much faster.

   📀Results: You can check the demo result and my test results [here]().

   

3. 1

4. 1

5. 1

6. 1

7. motion-diffusion-model

   Code: https://github.com/GuyTevet/motion-diffusion-model

   Projects Page: https://guytevet.github.io/mdm-page/

   Paper: https://arxiv.org/abs/2209.14916

   Results: 

8. 1

9. 1

10. 

## <span id='myanchor3'>3d human motion capture</span>



## <span id='myanchor4'>other</span>

