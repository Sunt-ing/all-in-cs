### GPU监控工具

|          | nvidia-smi（系统管理接口）                            | nvidia-settings    | wookayin/gpustat                     | wookayin/gpustat-web      | mountassir/gmonitor                                          |
| -------- | ----------------------------------------------------- | ------------------ | ------------------------------------ | ------------------------- | ------------------------------------------------------------ |
| 形式     | 命令行                                                | 命令行             | 命令行                               | web UI                    |                                                              |
| 局限性   | 只适用于NVIDIA<br />在某些Linux机器上失效（不适用）   |                    | 只适用于NVIDIA                       | 还处在初期，可能有不少bug | 适用于Ubuntu系统，没在别的系统中测试。理论上可以适用于多GPU的场景，但没实际试过 |
| 特点     |                                                       | nvidia-smi的替代品 | 是nvidia-smi的封装。可以周期性地监控 | 是gpustat的web UI         |                                                              |
| 监控指标 | GPU内存、GPU利用率、温度、GPU fan speed、GPU能耗等... |                    |                                      |                           | core usage, VRAM usage, PCI-E & memory bus usage、GPU温度    |
| 地址     |                                                       |                    | GitHub                               | GitHub                    | GitHub                                                       |
| 语言     |                                                       |                    | python                               |                           | C++                                                          |



### GPU监控可视化工具——只适用于某些平台

|          | NVDashboard                                                  | GPU shark       | intel-gpu-tools |
| -------- | ------------------------------------------------------------ | --------------- | --------------- |
| 介绍     |                                                              |                 |                 |
| 项目链接 | https://github.com/rapidsai/jupyterlab-nvdashboard           |                 |                 |
| 是否开源 | 是                                                           |                 |                 |
| 指标     | PCIe、GPU内存、GPU利用率                                     |                 |                 |
| 是否实时 | 是                                                           |                 |                 |
| 局限性   | 只适用于 `NVIDIA GPU`<br />是 JupyterLab的插件，只适用于`Jupyter`环境<br />不支持`Windows` | 只能用于Windows | 只能用于Intel   |

