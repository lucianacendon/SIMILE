
# REFERENCE
This session is intended to list all the parameters required by the Simile library in the context of setting-up your config files. <br>

## Config for Training
Required Headers:
```
    [DATA], [SIMILE], [OPERATION_OPTIONS]
```
Required Headers depending on choices:
```
    If autoreg_type = linear:
        [LIN_AUTOREG]
    If autoreg_type = geometric_velocity:
        [GEOMVEL_AUTOREG]
    If policy_type = xgboost:
        [XGBOOST]
    If policy_type = neuralnet:
        [NN]
```

### [ DATA ]
<b>train_file :</b> XML file with paths to training data (see 'Preparing your data' session on [README.md](https://github.com/lucianacendon/simile/blob/master/README.md)) <br>
<b>valid_file (OPTIONAL) : </b> XML file with paths to validation data (see 'Preparing your data' session on [README.md](https://github.com/lucianacendon/simile/blob/master/README.md)) <br> (<b>Note:</b> if not specified, the algorithm will proceed without a validation set) <br>
<b>n_features :</b> number of features used to describe environment <br>
<b>n_target :</b> number of target variables (actions) <br>
<b>target_labels :</b> target variable names for plotting. If multilple labels, you should write them in the same order from left-to-right as the order from left-to-right that the data is aranged on the data arrays. <br>
<b>model_dir :</b> directory where to save the model (Warning: I strongly advise to use a dedicated directory for each trained model to avoid conflicts)<br>
<b>model_label :</b> a short name to make it easy to identify the model. This label will become a tag to be added to every file generated by the model <br>
<b>init_value (OPTIONAL) :</b> fixed initial sequence value to be used by all episodes at rollout <br> (<b>Note:</b> if not specified, the inital value will be taken from the ground truth data) <br>

### [ SIMILE ]
<b>tao :</b> time-horizon <br>
<b>sigma :</b> initial smooth feedback parameter (sigma<sub>0</sub>). This parameter decays exponentially with the learning iterations, according with: <br>
    sigma = sigma<sub>0</sub> / 1.6<sup>n-1</sup> <br>

<b>lambd_smooth :</b> loss function regularization (controls closeness to smooth function)<br>
<b>n_it :</b> number of learning iterations <br>
<b>autoreg_type :</b> choice of smooth regularizer. Possible choices: <br>
    `average` : average of previous `tao-1` actions <br>
    `constant` : previous action <br>
    `linear` : prediction of a linear autoregressor trained to predict current action given previous `tao-1` actions <br>
    `geometric_velocity` : weighted velocity relationship between previous `tao-1` actions. <br>For `tao`=5: <br>
       a<sub>t</sub> = a<sub>t-1</sub> + γ ( a<sub>t-1</sub> - a<sub>t-2</sub> ) + γ<sup>2</sup> ( a<sub>t-1</sub> - a<sub>t-2</sub> ) + γ<sup>3</sup> ( a<sub>t-3</sub> - a<sub>t-4</sub> ) <br>

<b>policy_type :</b> choice of supervised model class. Possible choices: <br>
    `xgboost` : [parallel tree boosting](https://xgboost.readthedocs.io/en/latest/) <br>
    `neuralnet` : 2 layer feedforward neural net <br>
        
       
### [ OPERATION_OPTIONS ]
Note: all variables under this header are boolean <br>
<b>no_action_first_policy:</b> if True, the algorithm runs without including the expert demontration on the state formation of the initial policy <br>
<b>look_future:</b> if True, instead of using `tao` past features from environment as part of the state, it uses `tao/2` features from past and `tao/2` features from future. <br>
<b>only_env_feat:</b> if True, instead of running simile, it performs supervised learning using the model of your choice on only the envinroment features. This is useful to assess the predictive power of your environment features before proceeding with full training.<br>
<b>normalize_input:</b> if True, it normalizes input space to be in range (-0.5, 0.5)<br> 
<b>normalize_output:</b> if True, it normalizes output space to be in range (-0.5, 0.5) <br> 
<b>plot_train_results:</b> if True, it saves plot results from training session on training set. The results will be saved in a folder named `Log` inside the model directory. <br>
<b>plot_valid_results:</b> if True, it saves plot results from training session on validation set. The results are saved in the `Log` directory created inside the model directory.<br>

### [ XGBOOST ]
Note: required only if policy_type=xgboost.<br>
<b>n_estimators</b> : n_estimators from xgboost's XGBRegressor <br>
<b>regularization</b> : reg_lambda from xgboost's XGBRegressor <br>
<b>learning_rate</b> : learning_rate <br>

Note: XGBoost is very powerful have a lot more built-in variables w.r.t the above ones. If you find these are not enough to fit your data and would like to have more flexibility, you may wish to modify the xgboost model instantiated on file `Lib/parallel_tree_boosting.py`

### [ NN ]
Note: required only if policy_type=neuralnet.<br>
<b>hidsize_1</b> : n_estimators from xgboost's XGBRegressor <br>
<b>hidsize_2</b> : reg_lambda from xgboost's XGBRegressor <br>
<b>n_epoch</b> : number of epoches to run each simile learning iteration <br>
<b>save_freq</b> : frequency in number of epochs in which you wish to save learning parameters <br>
<b>batch_size</b> : integer between 1 and size of your data <br>
<b>learning_rate</b> : learning rate <bR>
<b>regularization</b> : l2 regularization <br>
    
Note: The default model is a 2-layer feedforward neural net. If you wish to add more layers (or remove layers), you can do so by changing function "build_network()" inside NeuralNet class. 

### [ LIN_AUTOREG ]
Note: required only if autoreg_type=linear. <br>
<b>regularization_autoreg</b> : regularization factor for the linear autoregressor

### [ GEOMVEL_AUTOREG ]
Note: required only if autoreg_type=geometric_velocity.<br>
<b>gamma:</b> value of γ in the relationship. For tao=5: <br>
   a<sub>t</sub> = a<sub>t-1</sub> + γ ( a<sub>t-1</sub> - a<sub>t-2</sub> ) + γ<sup>2</sup> ( a<sub>t-1</sub> - a<sub>t-2</sub> ) + γ<sup>3</sup> ( a<sub>t-3</sub> - a<sub>t-4</sub> )


## Config for Test
Required Headers:
```
    [DATA]
```
### [ DATA ]
<b>test_file</b> : XML file with paths to test data (see 'Preparing your data' session on [README.md](https://github.com/lucianacendon/simile/blob/master/README.md)) <br>
<b>with_gt</b> : if True, it indicates that the arrays listed on the `test_file` contain ground_truth information embedded on them (e.g, like the data on training and validation set). If False, it means it contains only environment features (X) <br>
<b>model_dir</b> : path to pre-trained model directory <br>
<b>model_label</b> : label chosen for the model <br>
<b>init_value</b> : </b> fixed initial sequence value to be used by all episodes <br> (<b>Note:</b> if not specified, the inital value will be taken from the ground truth data `if provided`) <br>
<b>output_dir</b> : directory where to save output plots <br>
<b>policy_load</b> : Possible choices: <br>
    `best_policy` : loads the policy estimated to be the best one, according to some criteria of smoothness and accuracy. <br>
    `integer in range [0, n. of trained policies]` : load a specific policy chosen by the user <br>
