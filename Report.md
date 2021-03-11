# DQN robot navigation (Project report)

[//]: # (Image References)

[image1]: ./documentation/image1.JPG "Problem image"
[image2]: ./documentation/image2.JPG "DQN update weights"
[image3]: ./documentation/image3.JPG "soft update"
[image4]: ./documentation/image4.JPG "mean score plot"
[image5]: ./documentation/image5.JPG "mean score logs"
[image6]: ./documentation/image6.JPG "dueling network"
[image7]: ./documentation/image7.JPG "loading model"

![Trained Agent][image1]

The aim of this file is to document all details regarding the implemantion of a DQN to solve the Problem defined [here](./README.md)

## Algortihm Description
The Deep Q-Learning algorithm implemented comprises of the following steps:

* We first initialize two networks with the following architecture:

| Layers      | Shape                             |
|-------------|-----------------------------------|
| Input Layer | input_size = State_size(37)       |
| FC layer 1  | (input=37, output=64)             |
| Activation F.| ReLU               |
| FC layer 2  | (input=64, output=64)             |
| Activation F.| ReLU               |
| FC layer 3  | (input=64, output=Action_size(4)) |


The optimizer is Adam with a `learning rate = 5e-4`

* Initialize the replay memory with a `10000 buffer size`.
    * In the replay memory we store an experience tuple = (S,A,R,S', done)
    * **Note:** S' stands for next state and done denotes if the episode has ended.

* The two networks initialized with the above architecture are:
    * Local action-value function Q-network.
    * Target action-value function Q-network.
    
    Both networks are initialized with random weights and the local Q-network is updated every `4 steps`. 
    
    **note:** We don't entirely update the weights, instead we use a soft-update approach following the below formula:

![][image3]

Where theta represent the weights of the networks and the Tau parameter defines what portion will be updated from local to target. In this project we used a `Tau=1e-3`

* To start learning we first acumulate in the replay memory a minimum quantity that's equal to the batch size to train the network. 
    * The `Batch size` we used in this project is: `64`

* To take the samples for filling the replay memory we use a epsilon-greedy policy. 
    * We used a decaying epsilon parameter, with a `decay rate = 0.995`
    * We started the epsilon value in epsilon = 1 (equiprobable policy) and we decay over episodes until an epsilon = 0.01.

* The main step in the Deep Q-learning algorithm is the update of weights. This update is done with the following formula:


![DQN update weights][image2]

* Gamma is the `discount factor`, which we set to: `0.99`

## Results

Finally, we trained the parameters and plot the results with a 100 window to see how our agent learns through out episodes. The result is the following:

![][image4]

### Score Logs:
![][image5]

We can see that we reach the 13 target score for solving this project in episode 500. Then at around episode 1000 we reach the maximum score for our implementation which was 16.27

We saved the weights of the Q-network in the [checkpoint.pth](./checkpoint.pth) file. To test that we saved them correctly we also performed a 100 episode avarage test based on the checkpoint. The results were great! As shown below:
![][image7]


## Future Ideas

Some of the ideas we can implement to improve the project even more are:

* Implement Double DQN, where we use two networks to estimate the Q-target instead of only one, hence the name.
* Implement dueling DQN, where we modify the last layers of our Q-network architecture as shown below:
![][image6]
* Implement Prioritize experience replay, the idea behind this replay buffer is to preserve samples that we think we learn most from. This way, we can squeeze all information possible from these samples. 

### Challenging alternative problem: Learning from Pixels

In the project, our agent learned from information such as its velocity, along with ray-based perception of objects around its forward direction (a 37 state size). However, a more challenging task would be to learn directly from pixels.

To solve this harder task, we can download a new Unity environment.  This environment is almost identical to this project environment, where the only difference is that the state is an 84 x 84 RGB image, corresponding to the agent's first-person view. 

Environment download based on operating system:
- Linux: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P1/Banana/VisualBanana_Linux.zip)
- Mac OSX: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P1/Banana/VisualBanana.app.zip)
- Windows (32-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P1/Banana/VisualBanana_Windows_x86.zip)
- Windows (64-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P1/Banana/VisualBanana_Windows_x86_64.zip)

Then, place the file in the root of this project, and unzip (or decompress) the file.  Next, open `Navigation_Pixels.ipynb` and follow the instructions to learn how to use the Python API to control the agent.

(_For AWS_) If you'd like to train the agent on AWS, you must follow the instructions to [set up X Server](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Training-on-Amazon-Web-Service.md), and then download the environment for the **Linux** operating system above.

