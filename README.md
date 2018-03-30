# LanczosGCE
## Requirements
Fortran(2003) + lapack
## Introduction
One should first have a knowdge of what [LanczosSubspace](https://github.com/HengyueLi/LanczosSubspace) object it is. This module is an extension of that. </br>&nbsp;
LanczosGCE is a Quantum Cluster Solver for fermion spin 1/2 system. It considers a Grand Canonical Ensemble(GCE) system. You can use this code to study a quantum cluster model(Hubbard model for instance) within several tens of lines of code. All kinds of interactions can be considered. The symmetry (  [H,N] = 0 , [H,S</sub>z</sub>]=0) of the system can be considered to increase the speed and save the memory.

## example
Here is a complete example (with only 24 statements in the main program) to solve a two-site Hubbard system. The ground state energy and degeneracy are shown. The system contains a hopping term and onsite Coulomb energy.


    program main
      use LanczosGCE
      implicit none

      TYPE(LA_GCE)::LanGCE
      type(table)     :: Ta
      type(Ham)       :: H

      integer::hpara(8)
      complex*16::v
      character*16::Intp

      ! Initialization of table   (see https://github.com/HengyueLi/Fermion_Table )
      ! In this example, the real symmetry of the system is 2. So one can use symmetry = 0/1/2
      ! symmetry = 0 / 1 / 2 dose not change the final result and all the calculation.
      ! But one should remenber that the higher value it is, the faster the calculation will be.(And
      ! also the smaller memorry it will use. How different it is? -> Very different! )
      call ta%Initialization( ns = 2 ,  symmetry = 2 ) ! check = 0 , 1 , 2




      ! Define the Hamiltonian of the system (see https://github.com/HengyueLi/FermionHamiltonian )
      call H%Initialization( ns = 2 )
      ! start append H terms  (needed)
      call h%StartAppendingInteraction()



      !! set the hopping term
      Intp     = "Hopping"
      hpara(1) = 0
      hpara(2) = 1
      v        = (1.0_8,0._8)
      call h%AppendingInteraction(InterType=Intp,InterPara=hpara,InterV=v)


      !! Set onsite Coulomb energy
      Intp     = "GlobalOnSiteU"
      v        = (4.0_8,0._8)
      call h%AppendingInteraction(InterType=Intp,InterPara=hpara,InterV=v)


      !Finished setting Hamiltonian
      call h%EndAppendingInteraction()



      ! Initialize the Lanczos solver
      ! IsReal_ is optional
      Call LanGCE%Initialization(Ta = Ta,H = H                   )
      !Call LanGCE%Initialization(Ta = Ta,H = H ,IsReal_ = .True. )


      ! diagonalization
      call LanGCE%SynchronizeWithHamiltonian()



      !   let us check the groud state energy first:
      write(*,*)"Ground state energy:" , LanGCE%GetEg()

      ! we can also check the degeneracy:
      write(*,*)"Degeneracy:", LanGCE%GetDe()


      ! The two-sites system can be solved analytically. One can check that himself.

      ! More useful functions can be found in source code directly.

    end
