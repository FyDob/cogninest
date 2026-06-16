# 04 Designing custom experiments

There is a variety of customization possibilities for the model. In this tutorial, you will learn how to adapt the model to your research question through the most common methods. If this tutorial does not cover your use case, please reach out to [fynn.dobler@fu-berlin.de](mailto:fynn.dobler@fu-berlin.de) for discussion and advice!

Broadly, there are 5 recommended ways of systematically changing model behaviour:

1) Adjusting model parameters. The e- and i-neuron models, the Artola-Bröcher-Singer synaptic plasticity rules and the density of inter- and intra-area synaptic connections rely on a variety of parameters, which can be freely varied during model initialization.
2) Adjusting fundamental model properties. These are fundamental changes to the morphology of the model. Please note that model functionality after such a change requires rigorous testing and analysis, and we cannot provide support for customized models at this point in time.
  - You can replace the custom Felix e- and i-neuron models, as well as the Artola-Bröcher-Singer synaptic plasticity rules with any existing NEST implementations.
  - You can modify the number of areas by changing the model initialization process.
  - You can modify the topography of the model (i.e. which areas share connections) by changing the connectivity matrix during model initialization.
3) Adjusting training/testing parameters. You can vary stimulus duration, inter-stimulus interval and the strength of the incoming current during stimulation. You can also vary which areas of the model receive correlated, uncorrelated or no input during training or testing.
4) Changing stimulus properties. You can define custom grounding patterns for training and testing. This allows you to freely vary the number of stimulated neurons, their topographic distribution and whether grounding patterns have overlapping or non-overlapping neurons.

The following sections provide an overview of possible modifications, and where and how to make them. At the end, you will train a congenitally blind model following the approach of [Tomasello et al., 2019](https://doi.org/10.1038/s41598-019-39864-1) and evaluate the outcome.

## 4.1 Adjusting model parameters

The following table provides an overview of core model parameters.

| Parameter         | Model part  | Where to modify   |
|   -----           |    -----    |      -----       |
|   k_1             | e-neurons   | network/area.py > __init__() |
|   tau_m           | e-neurons   | network/area.py > __init__() |
|   alpha           | e-neurons   | network/area.py > __init__() |
|   tau_adapt       | e-neurons   | network/area.py > __init__() |
|   k_2             | e-neurons   | network/area.py > __init__() |
|   k_1             | i-neurons   | network/area.py > __init__() |
|   tau_m           | i-neurons   | network/area.py > __init__() |
|   p               | E-E synapses (within area)  | network/area.py > __init__() |
|   std_x           | E-E synapses (within area) | network/area.py > __init__() |
|   std_y           | E-E synapses (within area) | network/area.py > __init__() |
|   mean_x          | E-E synapses (within area) | network/area.py > __init__() |
|   mean_y          | E-E synapses (within area) | network/area.py > __init__() |
|   rho             | E-E synapses (within area) | network/area.py > __init__() |
|   mask            | E-E synapses (within area) | network/area.py > __init__() |
|   p               | E-E synapses (between areas)  | network/network_building.py > FelixNet.connect_areas() |
|   std_x           | E-E synapses (between areas) | network/network_building.py > FelixNet.connect_areas() |
|   std_y           | E-E synapses (between areas) | network/network_building.py > FelixNet.connect_areas() |
|   mean_x          | E-E synapses (between areas) | network/network_building.py > FelixNet.connect_areas() |
|   mean_y          | E-E synapses (between areas) | network/network_building.py > FelixNet.connect_areas() |
|   rho             | E-E synapses (between areas) | network/network_building.py > FelixNet.connect_areas() |
|   mask            | E-E synapses (between areas) | network/network_building.py > FelixNet.connect_areas() |
|   p               | E-I synapses  | network/area.py > __init__() |
|   std_x           | E-I synapses  | network/area.py > __init__() |
|   std_y           | E-I synapses  | network/area.py > __init__() |
|   mean_x          | E-I synapses  | network/area.py > __init__() |
|   mean_y          | E-I synapses  | network/area.py > __init__() |
|   rho             | E-I synapses  | network/area.py > __init__() |
|   mask            | E-I synapses  | network/area.py > __init__() |
|   weight          | I-E synapses   | network/area.py > __init__() |
|   weight          | Global I-E synapse   | network/area.py > __init__() |

## 4.2 Adjusting fundamental model properties

| Property                    | Where to modify   |
|   -----                     |      -----        |
|   Number of areas           |      network/network_building.py > FelixNet.build_net()        |
|   Connectivity matrix       |      network/network_building.py > FelixNet.connect_areas()    |
|   Neuron model              |      network/area.py > __init__()                              |
|   Synapse model (within area) |       network/area.py > __init__()                              |
|   Synapse model (between area) |       network/network_building.py > FelixNet.connect_areas()   |

## 4.3 Adjusting training/testing parameters

| Parameter                             | Model part  | Where to modify   |
| ---                                   | ---         | ---               |
| Stimulus duration                     | ---         | ---               |
| Inter-stimulus interval               | ---         | ---               |
| Stimulation strength                  | ---         | ---               |

Setting correlated/uncorrelated/no input during training is a bit more involved than changing parameters. Input selection is handled in the selected training method of the model (see ```network/train_nonspecific.py``` for an example). During the training loop, pattern selection is passed through the variable ```stim_specs```.

### Correlated input during training

The default setting of ```network/train_nonspecific.py```. All primary input areas are stimulated with a pattern from their specific pattern vector. The 'correlated' part of this mode means that is V1 is stimulated with the pattern ```visu[3]```, all other areas are stimulated with the pattern with index ```3``` in their respective vectors.

```
stim_specs={'V1': {'neurons': visu[patt_no],
                  'I_stim': stim_strength},
           'M1_L': {'neurons': motor[patt_no],
                  'I_stim':  stim_strength},
           'A1': {'neurons': audi[patt_no],
                  'I_stim':  stim_strength},
           'M1_i': {'neurons': arti[patt_no],
                 'I_stim':  stim_strength}}
```

### Uncorrelated input during training

Contrary to correlated input, uncorrelated input means that an area is still stimulated, but the input is randomized at every presentation. For example, if you want M1_L to receive uncorrelated input, the stim_specs definition would look as follows:

```
stim_specs={'V1': {'neurons': visu[patt_no],
                  'I_stim': stim_strength},
           'M1_L': {'neurons': f.neurons2IDs(random.sample(list(range(0,625)), SIZE_PATTERN)),
                  'I_stim':  stim_strength},
           'A1': {'neurons': audi[patt_no],
                  'I_stim':  stim_strength},
           'M1_i': {'neurons': arti[patt_no],
                 'I_stim':  stim_strength}}
```

Note that this is the default logic for object/action word training: when learning an object word, M1_L receives uncorrelated stimulation, but when learning an action word, V1 receives uncorrelated stimulation.

It is recommended to write your own stim_spec management logic. You can find the object/action training logic for inspiration in ```utils/stim.py```.

### No input during training

Contrary to uncorrelated input, there is also the option to turn input off completely. In previous research, this has been used to emulate a disconnect between a primary sensory or motor brain area and its corresponding sensory or motor input modality (i.e. optic nerves for V1). The recommended way of doing so is by setting ```stim_strength``` to ```0```.

```
stim_specs={'V1': {'neurons': visu[patt_no],
                  'I_stim': 0},
           'M1_L': {'neurons': f.neurons2IDs(random.sample(list(range(0,625)), SIZE_PATTERN)),
                  'I_stim':  stim_strength},
           'A1': {'neurons': audi[patt_no],
                  'I_stim':  stim_strength},
           'M1_i': {'neurons': arti[patt_no],
                 'I_stim':  stim_strength}}
```

## 4.4 Changing stimulus properties

By default, running a simulation automatically generates grounding patterns. The number of neurons stimulated by a grounding pattern is determined by the value of ```SIZE_PATTERN```, set in ```config/config_training.py```. The number of grounding patterns to be generated is determined by the value of ```NB_PATTERN```, in the same config file.

To use custom grounding patterns instead, you can provide them as ```.txt``` files. By default, the program expects custom patterns to be stored in the following files:
```
config
| patterns_training
| - visu.txt        # Visual input
| - motor.txt       # Motor input
| - audi.txt        # Auditory input
| - arti.txt        # Articulatory input
```

Each of these files should contain N lines, with N being the number of patterns you want to train on. Each line in the file corresponds to a grounding pattern. **Important**: You have to manually adjust the value of NB_PATTERN in the config file when using custom patterns.

Grounding patterns are defined as follows:

```
42#123#366#368#445#496
```

with each number corresponding to a neuron ID. Within each area, neurons are numbered from 1-625. Numbering begins from the top left corner of the area and continues left-to-right, up-to-down. ```#``` is the expected separator. **Important**: neuron IDs should be arranged in ascending order, and must be within the range [1,625].

Otherwise, there are no constraints on pattern design.

The custom pattern reading function ```read_pattern_from_file(["visu.txt", "motor.txt", "audi.txt", "arti.txt"], plot_patterns=True)``` needs to be called after model initialization in ```main/main_training.py```.

## 4.5 Re-implementing Tomasello et al. (2019)
