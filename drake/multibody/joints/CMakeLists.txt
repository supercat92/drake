add_library(drakeJoints
        drake_joint.cc
        fixed_joint.cc
        helical_joint.cc
        prismatic_joint.cc
        quaternion_ball_joint.cc
        quaternion_floating_joint.cc
        revolute_joint.cc
        roll_pitch_yaw_floating_joint.cc)

target_link_libraries(drakeJoints
  drakeGeometryUtil
  Eigen3::Eigen)

drake_install_libraries(drakeJoints)
drake_install_headers(
        drake_joint.h
        drake_joint_impl.h
        fixed_axis_one_dof_joint.h
        fixed_joint.h
        helical_joint.h
        prismatic_joint.h
        quaternion_ball_joint.h
        quaternion_floating_joint.h
        revolute_joint.h
        roll_pitch_yaw_floating_joint.h
        floating_base_types.h)
