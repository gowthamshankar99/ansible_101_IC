# Job Slicing vs Fork in AWX

## Job Slicing
- Breaks down a single job template into multiple smaller **independent** jobs
- Each slice processes a **subset** of the inventory
- Useful for running jobs against large inventories by dividing them into manageable chunks
- The original job is split into `N` number of job slices specified by the user
- Each slice runs as a completely separate job
- Better for managing system resources and parallel execution across inventory

## Fork
- Specifies how many parallel processes Ansible should use for task execution
- Controlled by the `forks` parameter
- Operates within a **single** job execution
- Default fork value is usually 5
- Determines how many hosts Ansible will communicate with simultaneously
- Primarily affects performance within a single job

## Example
- If you have 1000 hosts and set job slices to 10, you'll get 10 separate jobs with ~100 hosts each
- If you have 1000 hosts and set forks to 10, you'll have one job that communicates with 10 hosts at a time