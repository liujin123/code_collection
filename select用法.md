``` 
fd_set rfds;
struct timeval timeout = {0};
int32_t login_timeout = 1*60*60;//一小时没有数据转发则退出
	while(1) {
		/* 设置收到数据包的超时时间 */
		timeout.tv_sec = 10;
		timeout.tv_usec = 0;

		/* 设置套接字集 */
		FD_ZERO(&rfds);
		FD_SET(g_server_fd, &rfds);
		FD_SET(g_client_fd, &rfds);

		/* 使用select监听两个套接字是否有数据到来 */
		ret = select(nfds, &rfds, NULL, NULL, &timeout);
		if (ret == -1) {
			if (errno == EINTR) {
				goto next;
			} else {
				break;
			}
		} else if (ret == 0) {
			/* select超时 */
			goto next;
		}
		/* 如果服务器有数据到来则转发至客户端 */
		if (FD_ISSET(g_server_fd, &rfds)) {
			sc_transmit();
		}
		/* 如果客户端有数据到来则转发至服务器 */
		if (FD_ISSET(g_client_fd, &rfds)) {
			cs_transmit();
		}

next:
		/* 检查会话是否空闲超时 */
		if (login_timeout > 0
		&& time(NULL) > g_last_input_time + login_timeout * 60) {
			SASH_LOG_INFO("session is timeout");
			exit(EXIT_SUCCESS);
		}
	}
	
```
