# Reflection on the PID Control Project

## Calculation of Steering Value

Steering value must be within the range \[-1, 1\], hence, the following function has been used to convert the total PID error to the steering value:

```f(x) = x / (1 + abs(x))```,

where ```x = - Kp * p_error - Ki * i_error - Kd * d_error```.

## Computation of Throttle Values

The throttle was maintained at a constant value of 0.3 for the purposes of this project.

## Effect of P-I-D Components

1. Increasing the proportional gain, Kp, leads to the controller becoming more responsive to the cross-track error. However, it also leads to overshoot and oscillations \[See ```videos/high_kp.m4v``` where ```Kp = 4```\]. This is expected because a high proportional gain leads to a stronger response for the same degree of error. This increases the likelihood of overshoot, and once overshoot happens, oscillations are bound to occur. In short, increasing proportional gain increases the responsiveness of the controller but also makes it less stable. Likewise, a low value of Kp leads to a controller whose response is not adequate to address the errors quickly enough, as can be seen in ```videos/low_kp.m4v``` where ```Kp = 0.04```.
2. Setting the integral gain, Ki, to 0 leads to the controller maintaining a small bias towards the right of the track center; see ```videos/zero_ki.m4v```. Increasing Ki leads to instability and sluggishness in the response of the controller. This is expected because the integral component of the error term builds up over time as errors get accumulated, and a very high gain for the integral error can lead to the response being so slow that it leads to oscillations. See ```videos/high_ki.m4v``` where ```Ki = 0.1```.
3. Without any derivative gain, Kd, the controller takes a longer time to settle at the desired value, because oscillations take time to die down. See ```videos/low_kd.m4v``` where ```Kd = 0.5```. The derivative gain adds damping to the system, allowing the oscillations to die down sooner. As such, higher derivative improves the stability of the system. However, this has a limit; if Kd is too high, excessive response due to small changes in the cross-track error can be amplified and lead to higher overshoot and instability. See ```videos/high_kd.m4v``` for an example with ```Kd = 50```.

## Tuning Procedure

The first tuning procedure attempted was Twiddle. However, in the absence of a way to automatically calculate the cross-track error and start the simulator multiple times from a clean slate, it proved very tedious to carry out manually. Hence, this algorithm was abandoned in favour of manual tuning.

Below, I describe the procedure followed for manual tuning. This procedure is inspired from the Wikipedia article on PID Control: https://en.wikipedia.org/wiki/PID_controller#Manual_tuning.

1. Set Ki and Kd to 0.
2. Increase Kp until the loop oscillates.
3. Set Kp to half of the value at which oscillation begins.
4. Increase Ki until any offset is corrected as quickly as desired.
5. Increase Kd, if required, until the loop is able to reach the desired value after a load disturbance as quickly as desired.

The above procedure was followed to reach close to the final value. After that, fine-tuning was done on the basis of the sum of squares of cross-track errors incurred over one full lap of the track.

## "Best" PID Gain Values

1. Kp = 0.4
2. Ki = 0.001
3. Kd = 5

See ```videos/best_params.m4v``` for the entire lap with the above parameters.

## Controlling Throttle to Improve Response

One area of improvement here is to control the throttle in order to improve the response. This is inspired by human driving, where the speed is reduced while steering the vehicle. Another PID controller can be used to control the speed by varying the throttle, and the desired value of the speed can be dependent on the steering angle of the vehicle; high values of the steering angle should result in lower speeds. This might improve the response while also allowing the vehicle to be driven at higher speeds during straight sections of the track.