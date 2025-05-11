### 1. Install

Ubuntu
```
sudo apt-get update

sudo apt install ca-certificates curl gnupg lsb-release

sudo apt install docker.io docker-compose

#查看安装版本
docker -v
```

卸载docker
```
sudo apt remove --purge docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc
```

安装mdo
```bash
docker pull mdolab/public:u20-gcc-ompi-latest
docker image ls
docker run -it --name <NAME给容器起名字> --mount "type=bind,src=<HOST_DIR:当前对绝对目录>,target=<MOUNT_DIR:/home/mdolabuser/mount/>" <IMAGE-id使用image的ID> /bin/bash
exitdocker start <NAME>docker exec -it <NAME> /bin/bash
```

### 2. Docker操作

```markdown
在windows系统上docker占用大量空间
1、停止docker
2、备份当前docker数据
打开Windows powershell
wsl --export docker-desktop-dat "E:\docker\data.tar"
dir E:\docker\
3、删除原来的数据
wsl --unregister docker-desktop-data
wsl --list -v
(会输出version信息，导入时要用到)
4、将刚才备份的数据导入
wsl --import docker-desktop-data "E:\docker\image\" "E:\docker\data.tar" --version 2
wsl --list -v
5、启动docker
```

以管理员方式进入docker

```bash
sudo docker exec -ti -u root MACHaero /bin/bash
```

以用户身份进入docker

```bash
sudo docker exec -it MACHaero /bin/bash
```

示例代码:
```python
# built-insimport unittest
import os
import copy
# MACH testing classfrom adflow import ADFLOW
# import the testing utilitiesimport reg_test_utils as utils
from reg_default_options import adflowDefOpts
from reg_aeroproblems import ap_naca0012_time_acc
import reg_test_classes
baseDir = os.path.dirname(os.path.abspath(__file__))
class TestSolve(reg_test_classes.RegTest):
    """    Tests that ADflow can converge the wing from the mdo tutorial using the euler    equation to the required accuracy as meassure by the norm of the residuals,    and states, and the accuracy of the functions    based on the old regression test 15    """    N_PROCS = 2    ref_file = "solve_rans_time_acc_naca0012.json"    def setUp(self):
        super().setUp()
        gridFile = os.path.join(baseDir, "../../input_files/naca0012_rans-L2.cgns")
        f = 10.0  # [Hz] Forcing frequency of the flow        period = 1.0 / f  # [sec]        nStepPerPeriod = 8        nPeriods = 1        nfineSteps = nStepPerPeriod * nPeriods
        dt = period / nStepPerPeriod  # [s] The actual timestep        options = copy.copy(adflowDefOpts)
        options.update(
            {
                "gridfile": gridFile,
                "outputdirectory": os.path.join(baseDir, "../output_files"),
                "writevolumesolution": False,
                "vis4": 0.025,
                "vis2": 0.5,
                "restrictionrelaxation": 0.5,
                "smoother": "DADI",
                "equationtype": "RANS",
                "equationmode": "unsteady",
                "timeIntegrationscheme": "BDF",
                "ntimestepsfine": nfineSteps,
                "deltat": dt,
                "nsubiterturb": 10,
                "nsubiter": 5,
                "useale": False,
                "usegridmotion": True,
                "cfl": 2.5,
                "cflcoarse": 1.2,
                "ncycles": 2000,
                "mgcycle": "3w",
                "mgstartlevel": 1,
                "monitorvariables": ["cpu", "resrho", "cl", "cd", "cmz"],
                "usenksolver": False,
                "useanksolver": False,
                "l2convergence": 1e-6,
                "l2convergencecoarse": 1e-4,
                "qmode": True,
                "alphafollowing": False,
                "blockSplitting": True,
                "useblockettes": False,
            }
        )
        # Setup aeroproblem        self.ap = copy.copy(ap_naca0012_time_acc)
        # Create the solver        self.CFDSolver = ADFLOW(options=options, debug=False)
        self.CFDSolver.addSlices("z", [0.5])
    def test_solve(self):
        # do the solve        self.CFDSolver(self.ap)
        # check if the solution failed        self.assert_solution_failure()
        # check its accuracy        utils.assert_functions_allclose(self.handler, self.CFDSolver, self.ap, tol=1e-8)
if __name__ == "__main__":
    unittest.main()
```

### 3. u20-gcc-ompi-stable镜像无法使用

解决办法：进入容器后重新编译adflow组件。

```markdown
1、修改/home/mdolabuser/repos/adflow/config/config.mk文件，把make j 4改成make j 2
2、执行 make clean
3、执行 make
4、执行 pip install .
5、OK
```