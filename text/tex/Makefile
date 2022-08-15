# useful shortcuts

.PHONY: pdf
pdf:
	@echo "running OpTeX on index.tex ..."
	optex index.tex

.PHONY: open
open: pdf
	@echo "opening PDF build ..."
	open index.pdf

.PHONY: copy
copy: pdf
	@echo "copying PDF build to the final destination ..."
	cp index.pdf ../Endler-Martin-ROS-2-Bachelors-Thesis-Latest.pdf
	cp index.pdf ../Using_ROS_2_for_High-Speed_Maneuvering_in_Autonomous_Driving.pdf

.PHONY: clean
clean:
	rm -rf index.log index.pdf index.ref textput.log
