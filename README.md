TASK'S DATE: 15.11.2017 - FINISHED: 15.11.2017 (EASY)  

TASK SHORT DESCRIPTION: 1186 ('Year(s) Given' stat in a Users Record > Fundraising tab is being calculated incorrectly. It needs to count a year only if a user has donated that year. (e.g if they only donated in 2012, it needs to be 1 Year and not 5 Years))

GITHUB REPOSITORY CODE: hotfix/task-1186-years-given-stat-fundraising

ORIGINAL WORK: https://github.com/BusinessBecause/network-site/tree/hotfix/task-1186-years-given-stat-fundraising

CHANGES

	IN FILES: 
		
		CHANGED CODE: 
		
			FROM:

				public function get_years_giving($id){
					$this->db->select_min('payment_date_received', 'min_date')
						->select_max('payment_date_received', 'max_date')
						->where('legacy_sub_record', 0)
						->where('payment_user_id', $id);

					$query = $this->db->get($this->_table);

					$result = $query->result();
					if(count($result)==1) {
						$min_date = (int)date('Y', strtotime($result[0]->min_date));
						$max_date = (int)date('Y', strtotime($result[0]->max_date));

						return $max_date - $min_date;
					} else {
						return 0;
					}
				}
			
			TO:
			
				public function get_years_giving($id)
				{
					$query = $this->db->select('Year(payment_date_received) as year, COUNT(*) as counted')
									->from($this->_table)
									->where('payment_user_id', $id)
									->where('legacy_sub_record', 0)
									->where('payment_date_received IS NOT NULL', NULL, false)
									->group_by('Year(payment_date_received)')
									->get();

					$result = $query->result();
					
					return count($result);
				}
		
