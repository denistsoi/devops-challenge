export GOPATH = $(PWD)

servers = calc_server_sqrt calc_server_add calc_server_square

$(servers): %: %.go src/calc_util/calc_util.go
	go build -o $@ $<

.PHONY: start_servers
start_servers: $(servers)
	# this one returns the square of 'a'
	# ret := a
	./calc_server_square -port 8080 \
		2>/dev/null &
	# this one transforms 'a' and 'b' via the 8080 server then adds them up
	# ret := square(a) + square(b)
	./calc_server_add -port 8081 \
		-squareServerUrl 'http://localhost:8080/compute/square' \
		2>/dev/null &
	# this one transforms 'a' and 'b' via the 8081 server then takes the square root of the result
	# ret := sqrt( square(a) + square(b) )
	./calc_server_sqrt -port 8082 \
		-adderServerUrl 'http://localhost:8081/compute/add' \
		2>/dev/null &

.PHONY: stop_servers
stop_servers:
	@killall -q $(servers) || true

.PHONY: send_req
send_req:
	curl 'http://localhost:8082/compute/sqrt?a=30&b=40'

.PHONY: clean
clean: stop_servers
	rm -f $(servers)
